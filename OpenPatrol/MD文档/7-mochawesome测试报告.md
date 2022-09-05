本文主要介绍mochawesome的使用，mocha+mochawesome用于生成完整的单元测试报告

## 1-mochawesome简介

[mochawesome](https://github.com/adamgruber/mochawesome)是一个生成自定义Javascript测试的报告库，通常都是配合mocha使用。也可以和 [mochawesome-report-generator](https://github.com/adamgruber/mochawesome-report-generator) 配合生成增强的 HTML/CSS报告。

## 2-基本使用流程

### 1-package.json

package.json scripts部分新增test设置

```javascript
"test": "./node_modules/mocha/bin/mocha --recursive -t 10000 -s 2000 --reporter mochawesome test/automation/cases/MochaDemo.js"
```

![image-20220620202651595](/Users/wulei/Library/Application Support/typora-user-images/image-20220620202651595.png)

参数解释：

**--recursive：递归子目录**
默认情况下，mocha查找glob ./test/.js，因此您可能希望将测试放在test/文件夹中。如果要包含子目录，请使用--recursive，因为./test/*.js只匹配第一级中的文件，test并且./test/**/.js只匹配第二级中的文件test。

**-t, --timeout <ms>**
指定测试用例超时，默认为2秒。要覆盖，您可以以毫秒为单位传递超时，或者使用s后缀ex：--timeout 2s或--timeout 2000等效的值传递。

**-s, --slow <ms>**
指定“slow test”测试阈值，默认为75毫秒。Mocha使用它来突出显示花费太长时间的测试用例。

**文件目录**

支持单文件/通配符：test/automation/Mocha*.js 代表Mocha开头的文件

文件目录：test/automation/  目录下所有的文件

### 2-运行

运行测试有两种方式：

1-npm run test :命令行运行

2-点击运行按钮

![image-20220620202913414](/Users/wulei/Library/Application Support/typora-user-images/image-20220620202913414.png)

通过：npm run test 运行后

![image-20220620203008306](/Users/wulei/Library/Application Support/typora-user-images/image-20220620203008306.png)

### 3-查看报告

默认报告路径：mochawesome-report/

![image-20220620202251434](/Users/wulei/Library/Application Support/typora-user-images/image-20220620202251434.png)

报告内容：

![image-20220620203810506](/Users/wulei/Library/Application Support/typora-user-images/image-20220620203810506.png)

## 3-增强测试报告

默认的报告内容展示的信息比较简单，只有代码部分的展示，但是如果出错时，我们希望看到请求/响应的信息。mochawesome提供了addContext方法可以给测试报告添加额外的自定义内容。

### 1-addContext()源码

源码：mochawesome/src/addContext.js

const addContext = require('mochawesome/addContext');
addContext有两个参数，第一个参数传递mocha的this实例，第二个参数是自定义信息。

![image-20220620204205315](/Users/wulei/Library/Application Support/typora-user-images/image-20220620204205315.png)

### 2-自定义方法

新建：src/utils/ReportFormat.js ，代码如下

```javascript
exports.add = function (obj) {
    let options ;
    if (obj !== null) {
        options = {
            title: '请求与响应信息',
            value: {
                请求URL: obj.config.method + " " + obj.config.baseURL + obj.config.url,
                请求Headers: obj.config.headers,
                请求体Body: obj.config.data,
                响应Headers: obj.headers,
                响应体Body: obj.data,
            }
        }
    } else
        options = {title: '请求与响应信息',value: {}};
    return options
}
```

![image-20220620204611671](/Users/wulei/Library/Application Support/typora-user-images/image-20220620204611671.png)

### 3-新建用例

新建：test/automation/cases/MochaEnhanceReportTest.js

```javascript
const axios = require('axios');
const utilFormat = require('../../../src/utils/ReportFormat');
const addContext = require('mochawesome/addContext');
const {describe,beforeEach,afterEach,it}=require('mocha');

describe('自定义报告TestSuite',function () {
    this.timeout(10000);
    let obj;//1-定义全局obj变量
    beforeEach(function () {
        obj = null;//2-beforeEach初始化
    })
    afterEach(function () {
        addContext(this, utilFormat.add(obj));//4-afterEach执行addContext方法传入格式化的数据
    });

    it('场景1：自定义报告', async function () {
        await axios.get("https://www.baidu.com/", {
        }).then(function (res) {
            obj = res;//3-response对象赋值给obj
        }).catch(function (error) {
            console.log(error);
        })
    })
})
```

![image-20220620205904226](/Users/wulei/Library/Application Support/typora-user-images/image-20220620205904226.png)

### 4-运行脚本

修改package.json，scripts部分，匹配到新增的用例

![image-20220620205317619](/Users/wulei/Library/Application Support/typora-user-images/image-20220620205317619.png)

### 5-运行后查看

![image-20220620205920636](/Users/wulei/Library/Application Support/typora-user-images/image-20220620205920636.png)

报告：

![image-20220620205958571](/Users/wulei/Library/Application Support/typora-user-images/image-20220620205958571.png)

## 4-自定义报告信息

mochawesome可以通过addContext进行附加信息的输出外，还可以通过[mochawesome-report-generator](https://github.com/adamgruber/mochawesome-report-generator) 进行更多的自定义报告。

### 1-package.json新增

```javascript
"report": "./node_modules/mocha/bin/mocha --recursive -t 10000 -s 2000 --reporter mochawesome test/automation/cases/Mocha*.js --reporter-options reportDir=test/report,reportFilename=reportFile,reportPageTitle=NewPageTitle,reportTitle=NewReportTitle,code=false,charts=true"
```

### 2-运行report命令

可以发现报告目录指定到test/report下

![image-20220621144812909](/Users/wulei/Library/Application Support/typora-user-images/image-20220621144812909.png)

查看 test/report/reportFile.html

![image-20220621145103496](/Users/wulei/Library/Application Support/typora-user-images/image-20220621145103496.png)

### 3-参数解释

[完整参数解释](--reporter-options reportDir=test/report,reportFilename=reportFile,reportPageTitle=NewPageTitle,reportTitle=NewReportTitle,code=false,charts=true)

可以对比上面的命令配置查看具体报告的差别：

| Flag                  | Type    | Default                    | Description                                     |
| --------------------- | ------- | -------------------------- | ----------------------------------------------- |
| -f, --reportFilename  | string  | mochawesome                | 测试报告文件名称(html,json)                     |
| -o, --reportDir       | string  | mochawesome-report         | 测试报告指定存放目录                            |
| -t, --reportTitle     | string  | mochawesome                | 测试报告左上角标题                              |
| -p, --reportPageTitle | string  | mochawesome-report         | 测试报告浏览器标题                              |
| -i, --inline          | boolean | false                      | 内联报告                                        |
| --cdn                 | boolean | false                      | CDN加载                                         |
| --assetsDir           | string  | /mochawesome-report/assets | assets目录                                      |
| --charts              | boolean | false                      | 是否展示圆饼图统计成功与失败                    |
| --code                | boolean | true                       | 是否展示源码，默认展示                          |
| --autoOpen            | boolean | false                      | 是否自动打开                                    |
| --overwrite           | boolean | true                       | 是否重写                                        |
| --timestamp, --ts     | string  |                            | 时间戳格式，默认：Tuesday, June 21, 2022 2:42pm |
| --showPassed          | boolean | true                       | 是否展示：通过的报告                            |
| --showFailed          | boolean | true                       | 是否展示：失败的报告                            |
| --showPending         | boolean | true                       | 是否展示：挂起的报告                            |
| --showSkipped         | boolean | false                      | 是否展示：跳过的报告                            |
| --showHooks           | string  | failed                     | 展示hooks函数信息                               |
| --saveJson            | boolean | false                      | 是否保存为json文件                              |
| --saveHtml            | boolean | true                       | 是否保存为html文件，默认是                      |
| --dev                 | boolean | false                      | 是否开启 dev mode                               |

## 5-分组

由于package.json命令可以指定运行的目录，所以通常的情况是：

->>测试用例目录分组

->>测试报告分组

->>设置不同的命令

### 1-将cases目录分组

cases目录下：Demo分组，Group1分组(复制一个用例进去即可)

![image-20220621152057838](/Users/wulei/Library/Application Support/typora-user-images/image-20220621152057838.png)

### 2-测试报告分组

test/report 创建对应的分组:Demo,Group1

![image-20220621151732484](/Users/wulei/Library/Application Support/typora-user-images/image-20220621151732484.png)

### 3-分组命令

package.json新增

```javascript
"demo_report": "./node_modules/mocha/bin/mocha --recursive -t 10000 -s 2000 --reporter mochawesome test/automation/cases/Demo/Mocha*.js --reporter-options reportDir=test/report/Demo",
"g1_report": "./node_modules/mocha/bin/mocha --recursive -t 10000 -s 2000 --reporter mochawesome test/automation/cases/Group1/ --reporter-options reportDir=test/report/Group1"
```

### 4-查看

分别运行上面新增的命令，查看报告

![image-20220621152310708](/Users/wulei/Library/Application Support/typora-user-images/image-20220621152310708.png)

## 6-合并

按照5的方法分组运行产生报告后，如果有些合并需求（例如：查看某几个分组的合并报告），[mochawesome-merge](https://github.com/Antontelesh/mochawesome-merge) 可以将多个分组的报告进行合并后展示。（说明：这些依赖创建工程时都已经引入）

### 1-合并报告

将Demo，Group1的报告合并，生成到mochawesome-report（说明：暂不支持指定目录）

![image-20220621153021463](/Users/wulei/Library/Application Support/typora-user-images/image-20220621153021463.png)

### 2-新增命令

package.json新增合并命令:

```javascript
"mocha_merge": "mochawesome-merge ./test/report/**/*.json > ./mochawesome-report/merge.json",
"merge_report": "npm run mocha_merge && marge ./mochawesome-report/merge.json "
```

注意此时的路径设置！

### 3-运行merge_report

![image-20220621153454440](/Users/wulei/Library/Application Support/typora-user-images/image-20220621153454440.png)

### 4-查看报告

![image-20220621153825204](/Users/wulei/Library/Application Support/typora-user-images/image-20220621153825204.png)

查看发现2个报告合并到一起展示：

![image-20220621153851466](/Users/wulei/Library/Application Support/typora-user-images/image-20220621153851466.png)