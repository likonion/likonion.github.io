---
layout: post
title: Puppeteer踩坑记
categories: 前端技术
description: 记录 puppeteer 使用中遇到的问题
tags: puppeteer node
cover: https://likonion-1254082995.cos.ap-chengdu.myqcloud.com/media/0*WHo7bG8yHKyt_nzn.png
---
* content
{:toc}
记录 puppeteer 使用中遇到的问题

### 等待请求结果

当网络请求返回太快时，用 `page.waitForResponse()` 来等待会不稳定或者会错过返回，所以使用 `page.on('response')` 来监听最可靠

```js
let resData = null;
sync function getRes (res) {
    if (res.url() === 'url' && res.status() === 200) {
        const resJson = await res.json();
        resData = resJson.data;
    }
}
page.on('response', getRes)
await page.click('selector');
for (let i = 0; i < 30; i++) {
    if (resData === null) {
        await page.waitFor(1000);
    } else {
        page.removeListener('response', getRes);
        break;
    }
}
```

```js
const get = new Promise(res =>
    page.on('response', async (res_i) => {
        if (res_i.url() === 'url' && res_i.status() === 200) {
            res(await res_i.text());
        }
    })
)
await page.goto(url);
let getData = await Promise.race([get, page.waitFor(15000)]);
```

### 设置页面中执行方法返回的 Promise 值

```js
const result = await page.$eval('selector', () => {
    return Promise.resolve('value');
});
```

### 获取点击后打开的新标签页

```js
//在点击按钮之前，事先定义一个promise，用于返回新tab的page对象
const newPagePromise = new Promise(res =>
    browser.once('targetcreated',
        target => res(target.page())
    )
);
await page.$eval('a', a => a.click());
//点击按钮后，等待新tab对象
let newPage = await newPagePromise;
```

### 获取IFRAME

```js
const frameElement = await page.waitForSelector('ID or Class or other');
const iframe = await frameElement.contentFrame();
```

### `page.click()` 尽量使用页面方法替换

因为 `page.click()` 会受到页面元素遮挡的影响，所以使用页面方法中的 `click()` 成功率更高

```js
// bad
await page.click('a');
// good
await page.$eval('a', a => a.click());
```

### 防止识别为爬虫

一些网站会通过检测 `window.navigator` 下的几个参数来判断是否是爬虫，可以通过以下设置避免识别

```js 
await page.evaluateOnNewDocument(() => {
    Object.defineProperties(navigator, { webdriver: { get: () => false } });
    window.navigator.chrome = { runtime: {}, };
    Object.defineProperty(navigator, 'languages', { get: () => ['en - US', 'en'] });
    Object.defineProperty(navigator, 'plugins', { get: () => [1, 2, 3, 4, 5, 6], });
})
await page.goto('url');
```

### 清空输入框的值

通过点击两次或者三次鼠标来选中输入框的值，然后再按删除键来删除

```js
await page.click('selector', {clickCount: 2});
await page.keyboard.press('Backspace');
```




