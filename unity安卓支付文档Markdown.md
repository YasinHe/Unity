# Unity支付宝以及微信支付相关文档

###### Created by 贺应鑫
###### Time 2017/4/13

## 注意事项

#### * unity与安卓交互所使用的第三方资源包，unity本身都需要引用进去，否则调用安卓代码会找不到类库
####  * 关于unity和安卓的交互方式之前有写过相关文档
***
## 支付宝支付部分
### 流程图
![image]( https://github.com/YasinHe/Unity/blob/master/md%E5%9B%BE1.png?raw=true )
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
![image](https://github.com/YasinHe/Unity/blob/master/md%E5%9B%BE2.png?raw=true)

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
