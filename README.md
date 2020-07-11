# qrcode-currency
## 说明
本项目是对`qrcode-generator`项目进行了修改，删除了里面的一些功能，增加了其他一些功能，目的是做出web端和小程序端通用二维码生成框架。

## 使用方法
 1. 配置了解，以下参数为默认值

```
    {
        // 需要变成二维码的文本
        text: "text",
        // 用于指定每一小块二维码长宽
        cellSize: 2,
        // 1 - 40 选择版本
        typeNumber: 4,
        // 黑块颜色
        colorDark: "#000000",
        // 亮块颜色
        colorLight: "#ffffff",
        // 纠错等级（见下面纠错等级）
        correctionLevel: QRCode.QRErrorCorrectionLevel.H,
        // 边距
        margin: 8
    };
```
2. 纠错等级

```
    {
        L: 1,
        M: 0,
        Q: 3,
        H: 2
    }
    调用方式 QRCode.CorrectionLevel.H 等
    纠错等级越高能够容纳的字符越少
```

3. 使用Demo （其中参数可以根据自己需要配置）
    + Web端
        - 返回 `DataURL` (颜色参数只能是十六进制 如：#000000)
  
        ```
            // 通过配置文件配置数据
            <div id="demo"></div>
            <script type="text/javascript" src="qrcode.js"></script>
            <script type="text/javascript">
                var qr =new QRCode({
                    text:"demo"
                });
                document.getElementById("demo").innerHTML  = "<img src='"+qr.dataURL()+"'></img>"
            </script>
        ```
        ```
            // 直接指定数据
            <div id="demo"></div>
            <script type="text/javascript" src="qrcode.js"></script>
            <script type="text/javascript">
                var qr =new QRCode("demo");
                document.getElementById("demo").innerHTML  = "<img src='"+qr.dataURL()+"'></img>"
            </script>
        ```
        ```
            // 动态添加数据
            <div id="demo"></div>
            <script type="text/javascript" src="qrcode.js"></script>
            <script type="text/javascript">
                var qr =new QRCode();
                qr.addData("demo");
                qr.make();
                document.getElementById("demo").innerHTML  = "<img src='"+qr.dataURL()+"'></img>"
            </script>
        ```
        
        ```
            // addData 和 make 返回原对象可以连着一起书写。
            <div id="demo"></div>
            <script type="text/javascript" src="qrcode.js"></script>
            <script type="text/javascript">
                var qr =new QRCode("demo");
                document.getElementById("demo").innerHTML  = "<img src='"+qr.addData("demo").make().dataURL()+"'></img>"
            </script>
        ```
        
        - 操作 `canvas` 画布 （颜色可以为任何类型）
        
        ```
            <canvas id="demo"></canvas>
            <script type="text/javascript" src="qrcode.js"></script>
            <script type="text/javascript">
                var qr =new QRCode({
                    text:"demo",
                });
                var c = document.getElementById("demo");
                // qr.height 表示二维码高度
                c.height = qr.height ;
                // qr.width 表示二维码宽度
                c.width = qr.width;
                var d = c.getContext("2d");
                qr.renderTo2dContext(d);
            </script>
            // 其余的用法和 DataURL 相同
        ```
    + 微信小程序端
        - 返回 `DataURL` (颜色参数只能是十六进制 如：#000000)
        
        ```
        // wxml 文件
           <image src="{{imgURL}}"></image>
        // js 文件
            import QRCode from "qrcode-currency";
            Page({
                data: {
                    imgURL:""
                },
                onReady: function () {
                    const qrcode = new QRCode({
                        text:"demo",
                        colorDark: "#9ff312",
                        margin:10,
                        cellSize:8
                    });
                    this.setData({
                    imgURL:qrcode.dataURL()
                    })
                }
            })
        // 其他的使用方法和上面类似
        ```

        - 操作 `canvas` 画布 （颜色可以为任何类型）

        ```
        // wxml 文件
            <canvas type="2d" id="qrcode"></canvas>
        // js 文件
        // 其他的使用方法和上面类似
            import QRCode from "qrcode-currency";
            Page({
            onReady: function () {
                const qrcode = new QRCode({
                text:"demo",
                margin:10,
                cellSize:8
                });
                // 下面这些代码都是获取 context 对象和设置画布大小
                const query = wx.createSelectorQuery()
                query.select('#qrcode')
                .fields({ node: true, size: true })
                .exec((res) => {
                    const canvas = res[0].node
                    const ctx = canvas.getContext('2d')
                    const dpr = wx.getSystemInfoSync().pixelRatio
                    canvas.width = qrcode.width * dpr
                    canvas.height = qrcode.height * dpr
                    // 把 wxml 中 canvas 的 context 对象交给框架
                    qrcode.renderTo2dContext(ctx)
                })
            }
            })
        ```
    