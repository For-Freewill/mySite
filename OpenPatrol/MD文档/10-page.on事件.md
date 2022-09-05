本文主要讲解page.on的事件监听处理，未后续进行接口巡检做准备

# page.on事件监听

[Events事件](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v13.5.2&show=api-event-close)

**puppeteer提供了下列类型的浏览器事件监听：**

- [page.on('close')](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v13.5.2&show=api-event-close)
- [page.on('console')](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v13.5.2&show=api-event-console)
- [page.on('dialog')](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v13.5.2&show=api-event-dialog)
- [page.on('domcontentloaded')](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v13.5.2&show=api-event-domcontentloaded)
- [page.on('error')](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v13.5.2&show=api-event-error)
- [page.on('frameattached')](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v13.5.2&show=api-event-frameattached)
- [page.on('framedetached')](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v13.5.2&show=api-event-framedetached)
- [page.on('framenavigated')](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v13.5.2&show=api-event-framenavigated)
- [page.on('load')](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v13.5.2&show=api-event-load)
- [page.on('metrics')](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v13.5.2&show=api-event-metrics)
- [page.on('pageerror')](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v13.5.2&show=api-event-pageerror)
- [page.on('request')](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v13.5.2&show=api-event-request)
- [page.on('requestfailed')](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v13.5.2&show=api-event-requestfailed)
- [page.on('requestfinished')](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v13.5.2&show=api-event-requestfinished)
- [page.on('response')](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v13.5.2&show=api-event-response)
- [page.on('workercreated')](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v13.5.2&show=api-event-workercreated)
- [page.on('workerdestroyed')](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v13.5.2&show=api-event-workerdestroyed)

**什么是浏览器事件？**

[浏览器事件](https://developer.mozilla.org/zh-CN/docs/Web/API/Event)是指浏览器的生命周期中发生的一系列操作，例如加载，关闭，console输出信息。puppeteer封装了常见的浏览器事件监听，可以方便的在浏览器的指定事件发生时进行自定义操作。

下面介绍一些最常见的事件监听的应用：

| 事件                                                         | 触发                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [page.on('load')](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v14.0.0&show=api-event-load) | 当页面的 [load](https://developer.mozilla.org/en-US/docs/Web/Events/load) 事件被触发时触发。 |
| [page.on('console')](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v14.0.0&show=api-event-console) | 当页面js代码调用了 console 的某个方法，比如 console.log，或者 console.dir 的时候触发。 |
| [page.on('pageerror')](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v14.0.0&show=api-event-pageerror) | 当发生页面js代码没有捕获的异常时触发。                       |
| [page.on('requestfinished')](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v14.0.0&show=api-event-requestfinished) | 当页面的某个/全部请求成功完成时触发。                        |
| [page.on('response')](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v14.0.0&show=api-event-response) | 当页面的某个请求接收到对应的 [response](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v14.0.0&show=api-class-response) 时触发。 |
| [page.on('request')](https://zhaoqize.github.io/puppeteer-api-zh_CN/#?product=Puppeteer&version=v14.0.0&show=api-event-request) |                                                              |

## 1-page.on('load')

场景：监听页面的load事件触发

应用：暂无

新建：test/automation/cases/Demo/PageOnTest.js

```javascript
const {describe,it,before,after,afterEach}=require('mocha');
const Base=require('../../../Base');
const {assert}=require('chai');

describe('Page事件监听',function () {
    before(Base.before);
    // after(Base.after);
    afterEach(Base.afterEach);
    this.timeout(40000);
    it('Load事件', async function () {
        //load事件
        await page.on('load',async load=>{
            console.log('load event');
        });
        console.log('开始登陆')
        //1.博客园
        await page.goto("https://account.cnblogs.com/signin");
        //3. 输入：用户名
        await page.focus('#mat-input-0');
        await page.type('#mat-input-0','UI自动化');
        //4. 输入：密码
        await page.focus('#mat-input-1');
        await page.type('#mat-input-1','520bokeyuan');
    })
})
```

运行后发现先输出开始登陆，等浏览器load后，打印了load日志

![image-20220622103006245](/Users/wulei/Library/Application Support/typora-user-images/image-20220622103006245.png)

## 2-page.on('console')

场景：监听页面的load事件触发

应用：页面巡检

**说明：浏览器console很多错误都在这里展示，页面巡检需要重点关注的一部分**

新增it：test/automation/cases/Demo/PageOnTest.js

```javascript
it('2-console事件', async function () {
    //console
    await page.on('console',async console_msg=>{
        console.log("console output：type="+console_msg.type()+",msg="+console_msg.text())
    });
    console.log('开始登陆')
    //1.博客园
    await page.goto("https://account.cnblogs.com/signin");
    //3. 输入：用户名
    await page.focus('#mat-input-0');
    await page.type('#mat-input-0','UI自动化');
    //4. 输入：密码
    await page.focus('#mat-input-1');
    await page.type('#mat-input-1','520bokeyuan');
})
```



![image-20220622103622305](/Users/wulei/Library/Application Support/typora-user-images/image-20220622103622305.png)

可以看到console有输出当前时间：

![image-20220622103412708](/Users/wulei/Library/Application Support/typora-user-images/image-20220622103412708.png)

## 3-page.on('pageerror')

场景：当发生页面js代码没有捕获的异常时触发。巡检page页面异常

应用：页面巡检

**说明：pageerror页面巡检最重要的一部分，代表页面已经出现了异常**

新增it：test/automation/cases/Demo/PageOnTest.js

```javascript
    it('3-pageerror事件', async function () {
        //pageerror
        await page.on('pageerror',async error=>{
            console.log("page error"+error);
        });
        console.log('开始登陆')
        //1.博客园
        await page.goto("https://account.cnblogs.com/signin");
        //3. 输入：用户名
        await page.focus('#mat-input-0');
        await page.type('#mat-input-0','UI自动化');
        //4. 输入：密码
        await page.focus('#mat-input-1');
        await page.type('#mat-input-1','520bokeyuan');
    })
```

由于pageerror是偶发事件，这里找一个截图

![image-20220622104306698](/Users/wulei/Library/Application Support/typora-user-images/image-20220622104306698.png)

## 4-page.on('requestfinished')

场景：当页面发生请求时触发，请求的类型可以是图片，xhr，字体等

应用：页面巡检，接口巡检

**说明：requestfinished代表全部浏览器开发者工具Network里的全部请求**

新增it：test/automation/cases/Demo/PageOnTest.js

```javascript
    it('4-requestfinished事件', async function () {
        //requestfinished
        await page.on('requestfinished',async request => {
            const res = request.response();
            if (res.status()==200) {
                console.log("请求="+request.url()+"，请求数据="+request.postData()+"，返回状态="+res.status());
                res.text().then(text => {
                    console.log("返回体="+text);
                });
            }
        });
        //1.博客园
        await page.goto("https://account.cnblogs.com/signin");
```

requestfinished事件非常的多，一般的用法是进行x h r类型的判断

```javascript
await page.on('requestfinished',async request => {
            const res = request.response();
            if (request.resourceType() == "xhr" && res.status()!==200) {
                console.log("接口="+request.url()+"，请求数据="+request.postData()+"，返回状态="+res.status());
                res.text().then(text => {
                    console.log("返回体="+text);
                });
            }
        });
```

resourceType的类型：

('Document' | 'Stylesheet' | 'Image' | 'Media' | 'Font' | 'Script' | 'TextTrack' | 'XHR' | 'Fetch' | 'EventSource' | 'WebSocket' | 'Manifest' | 'SignedExchange' | 'Ping' | 'CSPViolationReport' | 'Preflight' | 'Other');



![image-20220622105730711](/Users/wulei/Library/Application Support/typora-user-images/image-20220622105730711.png)

## 5-page.on('response')

场景：当接口有返回时监听，可以获取接口的返回和状态

应用：接口巡检

**说明：response代表指定请求的响应监听，接口巡检的底层**

新增it：test/automation/cases/Demo/PageOnTest.js

```javascript
it('5-response事件', async function () {
        //1. 登陆：博客园
        await page.goto("https://account.cnblogs.com/signin");
        //2. 点击：密码登陆
        await page.waitForSelector('#mat-tab-label-0-0 > div',{timeout:2*1000}).then(ele=>{ele.click()}).catch(e=>{console.log(e)})
        //3. 输入：用户名
        await page.focus('#mat-input-0');
        await page.type('#mat-input-0','UI自动化');
        //4. 输入：密码
        await page.focus('#mat-input-1');
        await page.type('#mat-input-1','520bokeyuan');
        //5. 勾选：记住我（可选）
        // await page.click('#mat-checkbox-1 > label > span.mat-checkbox-inner-container');
        //6. 点击：登陆
        await page.waitForSelector('body > app-root > app-sign-in-layout > div > div > app-sign-in > app-content-container > div > div > div > form > div > button',{timeout:2*1000}).then(ele=>{ele.click()}).catch(e=>{console.log(e)})
        //response
        await page.on('response',async res => {
            if(res.url().indexOf("https://www.cnblogs.com/aggsite/allsitecategories")!==-1 &&res.status() === 200){
                res.text().then(text => {
                    console.log("返回体="+text);
                });
            }
        });
        //等待响应返回200
        await page.waitForResponse(response => response.status() === 200);
        await page.waitForTimeout(2000);
        await page.goto("https://home.cnblogs.com/u/2910916");
    })
```

可以看到制定接口的返回信息：

![image-20220622113735864](/Users/wulei/Library/Application Support/typora-user-images/image-20220622113735864.png)

## 6-page.on('request')

场景：对指定请求进行监听，可以修改请求或者mock请求返回

应用：请求拦截，Mock

**说明：request代表指定请求的操作，多用于请求拦截或者接口Mock**

• 监听的请求的类型resourceType的类型：

('Document' | 'Stylesheet' | 'Image' | 'Media' | 'Font' | 'Script' | 'TextTrack' | 'XHR' | 'Fetch' | 'EventSource' | 'WebSocket' | 'Manifest' | 'SignedExchange' | 'Ping' | 'CSPViolationReport' | 'Preflight' | 'Other');

• await page.setRequestInterception(true) 开启拦截，必须先开启;

• await req.respond() 返回一个自定义响应（Mock返回）；

• await req.abort() 继续请求；

• await req.continue() 继续请求；

• await req.respond()和await req.continue() 不能同时调用；

• 使用await req.respond() 拦截返回值注意跨域；

• 使用await req.respond() 拦截返回值注意返回json或字符串

### 1-请求拦截

⚠️注意：

**1-request监听之前必须先开启拦截：page.setRequestInterception(true)** 

**2-其他请求一定要放行：request.continue()**

新增it：test/automation/cases/Demo/PageOnTest.js

```javascript
it('6-request事件-请求拦截', async function () {
        //1. 登陆：博客园
        await page.goto("https://account.cnblogs.com/signin");
        //2. 点击：密码登陆
        await page.waitForSelector('#mat-tab-label-0-0 > div',{timeout:2*1000}).then(ele=>{ele.click()}).catch(e=>{console.log(e)})
        //3. 输入：用户名
        await page.focus('#mat-input-0');
        await page.type('#mat-input-0','UI自动化');
        //4. 输入：密码
        await page.focus('#mat-input-1');
        await page.type('#mat-input-1','520bokeyuan');
        //5. 勾选：记住我（可选）
        // await page.click('#mat-checkbox-1 > label > span.mat-checkbox-inner-container');
        //6. 点击：登陆
        await page.waitForSelector('body > app-root > app-sign-in-layout > div > div > app-sign-in > app-content-container > div > div > div > form > div > button',{timeout:2*1000}).then(ele=>{ele.click()}).catch(e=>{console.log(e)})
        //request：拦截图片请求
        await page.setRequestInterception(true);//开启请求拦截
        await page.on('request',async request => {
            if(request.resourceType()==='image'){
                return request.abort();//图片请求，直接丢弃
            }else{
                return request.continue();//其他请求，继续
            }
        });
        //等待响应返回200
        await page.waitForResponse(response => response.status() === 200);
        await page.waitForTimeout(2000);
        await page.goto("https://home.cnblogs.com/u/2910916");
    })
```

可以看到图片请求被拦截：

![image-20220622144219425](/Users/wulei/Library/Application Support/typora-user-images/image-20220622144219425.png)

### 2-Mock接口返回

在5-page.on('response')中我们知道（https://www.cnblogs.com/aggsite/allsitecategories）接口返回的是一组列表

![image-20220622144937844](/Users/wulei/Library/Application Support/typora-user-images/image-20220622144937844.png)

这里我们可以将这个接口的返回改成自定义信息

{"code":0,"msg":""}

新增it：test/automation/cases/Demo/PageOnTest.js

```javascript
it('6-request事件-Mock', async function () {
    //1. 登陆：博客园
    await page.goto("https://account.cnblogs.com/signin");
    //2. 点击：密码登陆
    await page.waitForSelector('#mat-tab-label-0-0 > div',{timeout:2*1000}).then(ele=>{ele.click()}).catch(e=>{console.log(e)})
    //3. 输入：用户名
    await page.focus('#mat-input-0');
    await page.type('#mat-input-0','UI自动化');
    //4. 输入：密码
    await page.focus('#mat-input-1');
    await page.type('#mat-input-1','520bokeyuan');
    //5. 勾选：记住我（可选）
    // await page.click('#mat-checkbox-1 > label > span.mat-checkbox-inner-container');
    //6. 点击：登陆
    await page.waitForSelector('body > app-root > app-sign-in-layout > div > div > app-sign-in > app-content-container > div > div > div > form > div > button',{timeout:2*1000}).then(ele=>{ele.click()}).catch(e=>{console.log(e)})
    //request
    await page.setRequestInterception(true);//开启请求拦截
    await page.on('request',async request => {
        if(request.url()==="https://www.cnblogs.com/aggsite/allsitecategories"){
            await request.respond({//自定义返回
                status: 200,
                headers: {
                    'Access-Control-Allow-Origin': '*',
                },
                contentType: 'application/json; charset=utf-8',
                body: JSON.stringify({"code":0,"msg":""})}
                );
        }else{
            return request.continue();//其他请求，继续
        }
    });
    //等待响应返回200
    await page.waitForResponse(response => response.status() === 200);
    await page.waitForTimeout(2000);
})
```

查看请求：

![image-20220622145512960](/Users/wulei/Library/Application Support/typora-user-images/image-20220622145512960.png)

## 7-全局监听

对于一些重要的事件监听可以进行全局监听（例如：console，pageerror），因此可以在初始化页面时就进行监听。

修改：src/system/init.js 的createPage方法，加入监听事件：

```javascript
createPage: async function(browser) {//创建页面
        if (global.config.remote == false) {//默认情况，本地
            const page = await browser.newPage();
            await page.setViewport({
                'width': 1280,
                'height': 800
            });
            page.setDefaultNavigationTimeout(global.config.timeout);
            let targets = await browser.targets();
            const targetPages = await targets.filter(target => target.type() === 'page');
            //如果打开了多个页面，那么就要切换到最后一个页面
            if (targetPages.length > 2) {
                await targetPages[targetPages.length - 1].page();
            }
            global.page = page;
            //监听load事件
            page.on('load',  async load => {
                console.log("load stared");
            });
            //监听console事件:可以按照类型进行过滤
            page.on('console',  async console_msg => {
                if(console_msg.type()==='error'){
                    console.log("console error output msg="+console_msg.text());
                }
            });
            //监听page error事件
            page.on('pageerror',  async error_msg => {
                console.log("page error="+error_msg);
            });
            //监听request finished事件，对事件进行输出
            page.on('requestfinished',  async request => {
                const res = request.response();
                if (request.resourceType() == "xhr" && res.status()!==200) {
                    console.log("接口="+request.url()+"，请求数据="+request.postData()+"，返回状态="+res.status());
                    res.text().then(text => {
                        console.log("返回体="+text);
                    });
                }
            });
            return page;
        } else {
            const pages = await browser.pages();
            const page = pages[0];
            page.setViewport({
                'width': 1280,
                'height': 800
            });
            page.setDefaultNavigationTimeout(global.config.timeout);
            let targets = await browser.targets();
            const targetPages = await targets.filter(target => target.type() === 'page');
            //如果打开了多个页面，那么就要切换到最后一个页面
            if (targetPages.length > 2) {
                await targetPages[targetPages.length - 1].page();
            }
            global.page = page;
            //监听load事件
            page.on('load',  async load => {
                console.log("load stared");
            });
            //监听console事件:可以按照类型进行过滤
            page.on('console',  async console_msg => {
                if(console_msg.type()==='error'){
                    console.log("console error output msg="+console_msg.text());
                }
            });
            //监听page error事件
            page.on('pageerror',  async error_msg => {
                console.log("page error="+error_msg);
            });
            //监听request finished事件，对事件进行输出
            page.on('requestfinished',  async request => {
                const res = request.response();
                if (request.resourceType() == "xhr" && res.status()!==200) {
                    console.log("接口="+request.url()+"，请求数据="+request.postData()+"，返回状态="+res.status());
                    res.text().then(text => {
                        console.log("返回体="+text);
                    });
                }
            });

            return page;
        }
    }
```

运行：test/automation/cases/Login/LoginTest.js

![image-20220622150437119](/Users/wulei/Library/Application Support/typora-user-images/image-20220622150437119.png)

