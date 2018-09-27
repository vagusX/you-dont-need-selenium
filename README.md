# you-dont-need-selenium
> puppeteer e2e cheatsheet

* **getComputedStyles**

```js
const btnStyles = await page.$eval('.button', btn => JSON.parse(JSON.stringify(getComputedStyle(btn))));
```

referrence: https://github.com/GoogleChrome/puppeteer/issues/696

* **getInputValue**

```js
const input = await page.$('input');
const valueHandle = await input.getProperty('value');
const value = await valueHandle.jsonValue();
```

referrence: https://github.com/GoogleChrome/puppeteer/issues/438

* **getTextContent**

```js
const text = await page.$eval('.text', el => el.textContent);
```

* **elmentIfExistsOrNot**
> use `expect-puppeteer`

```js
const el = await page.$('.button');
expect(el).not.toBeNull();

const els = await page.$$('.button');
expect(els.length).toBeGreaterThan(0);
```

If `.button` not existed, the return value will be null.
So you can just expect the return value is null or not.
If no elements match the selector, the return value resolve to [].

* **scroll**
> use $eval

```js
  await page.evaluate(() => window.scrollBy(0, document.body.scrollHeight));

  await page.$eval('.button', btn => window.scrollTo(0, btn.scrollHeight));

  await page.$eval('.button', btn => btn.scrollIntoView());

  await page.$eval('.button', btn => btn.scrollIntoViewIfNeeded());
```

* **getLocation**

```js
await page.url(); // doesnot work under history API
await page.evaluate(() => location.href);
```
if you click a element trigger a navigation event, there's a separate `page.waitForNavigation()` promise to be resolved, and ending up with a race condition that yeiled unexpected results.
Here is the correct pattern:

```js
const [response] = await Promise.all([
  page.waitForNavigation(waitOptions),
  page.click(selector, clickOptions),
]);
```

"navigation" happens only whenever the URL of the page changes. Just sending requests doesn't trigger any navigation, thus "waitForNavigation" doesn't work.

```js
await page.goto(url, {waitUntil: 'networkidle'});
const url = await page.evaluate('location.href');
```

* **waitForNavigation**
only works for location change and history change, but HashRouter/MemoryRouter doesn't works


* **changeHash**
```js
await page.goto('/home.html#123');
await page.goto('/home.html#456');
```
