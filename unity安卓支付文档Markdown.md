# Unity支付宝以及微信支付相关文档

###### Created by 贺应鑫
###### Time 2017/4/13

## 注意事项

#### * unity与安卓交互所使用的第三方资源包，unity本身都需要引用进去，否则调用安卓代码会找不到类库
####  * 关于unity和安卓的交互方式之前有写过相关文档
***
## 支付宝支付部分
### 流程图
```
graph TD
    A[根据商品服务端发起订单] -->B(订单信息交给客户端)
    B -->C(客户端调用支付宝支付获取返回信息)
    C -->D{客户端结果}
    D -->|成功| E[服务端请求验证订单存根]
    E -->F(成功返回商品or实际并没有支付成功)
    D -->|失败| G[支付失败提示]
    D -->|取消| H[支付取消]
    D -->|确认中| I[订单正在确认]
```
### unity调用安卓支付代码:

``` C#
try {
            AndroidJavaClass testActivityClass = new AndroidJavaClass("com.XunDa.Recognition.SecActivity");
            if (testActivityClass != null) {
                Debug.LogWarning("testActivityClass note null");
                testActivityClass.CallStatic("pay", "PayInfo(服务端返回的支付信息)");
            }
        }catch(System.Exception e){
                Debug.LogError(e);
        }
```
### 安卓端返回给unity的接口代码：

``` Java
UnityPlayer.UnitySendMessage("DLScript", "PayResult", "succ");
```

### unity需要开放给java的接口:

``` C#
//DLScript是视图对象(按房尚平的说法，需要把脚本跟视图对象关联，该接口方法就得写在这个DLScript下)
public void PayResult(string result)
{
    //result 分为  succ  fail   cancel   confirm四种情况
}
```
*unity获取到安卓结果，如果是成功就请求后台接口查看订单存根是否真的收到钱，选择下达商品或者当做失败处理*

---

## 微信支付部分
### 流程图
```
graph TD
    A[根据商品服务端发起订单] 
    -->B(订单按需要字段传给安卓)
    B -->C(客户端调用winxin支付)
    C -->D(微信会根据notify_url回传给服务端请求支付情况)
    D -->F(服务端返回应答weixin服务器)
    F -->G{客户端结果}
    G -->|成功| H[支付成功]
    G -->|失败| I[支付失败提示]
    G -->|取消| J[支付取消]
```

### 安卓端接收unity的方法接口：

``` Java
//这些字段请求订单时都会返回，app_id是后台申请应用后定死的
public static void WinXinPay(String app_id,String partnerid,String prepayId,String nonceStr,String timeStamp,String packageValue,String sign){
}
```
### unity需要提供给安卓的微信支付接口：

``` C#
public void WinXinPayResult(string result)
{
    //result 分为  succ  fail   cancel   failUnknown
}

```

### 目前微信支付进度
- [x] 已完成
    - [x] 安卓接口开放以及处理调用微信
    - [x] 安卓的返回unity
- [ ] 未完成
    - [ ]  unity中加入winxin资源包，脚本中未支持安卓调用接口
    - [ ]  目前应用申请未下来，支付服务等暂无，没有调试过


## 相关参考链接
#### 微信支付
[统一下单](https://pay.weixin.qq.com/wiki/doc/api/app/app.php?chapter=9_1)

[服务端响应微信支付](https://pay.weixin.qq.com/wiki/doc/api/app/app.php?chapter=9_7&index=3)

#### 支付宝(这个测试目前已经调通了)
[开发文档](https://doc.open.alipay.com/docs/doc.htm?treeId=204&articleId=105051&docType=1)
