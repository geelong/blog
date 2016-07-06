在使用startActivityForResult关联俩个Activity中，发现A跳转到B，B设置setResult之后，A没有执行onActivityResult，查找一下，发现是A,B之前的启动模式都使用singleTask，查找下相关问题的解决办法。问题搞定了，下面也总结下之前自己遇到的其他情况。 
总结下出现问题和解决办法：

如果Activity 的加载启动模式(launchMode )是SingleTask或者 ，会导致onActivityResult()没有调用。修改启动模式为singleTop或者标准。原因是：设置为 launchmode=”SingleTask”，系统将在 startActivityForResult() 后直接调用 onActivityResult()。

两个activity传递数据和返回数据时，请求方的onActivityResult始终无响应，通过debug调试模式也没见调用该方法。查看了各种配置和程序代码，均未发现有错误之处。后来仔细阅读API说明，恍然大悟，原来是调用startActivityForResult的参数问题，即调用时这样： 
startActivityForResult(intent, 0); 
是第二个参数的问题，该参数必须大于0才能在返回值，并激活onActivityResult方法。 
API描述： 
@requestCode If >= 0, this code will be returned in onActivityResult() when the activity exits.

Fragment 的onActivityResult() 不执行。 
解决方法：处理方法是在与Fragment绑定的FragmentActivity中重写onActivityResult方法。返回的result先传至Fragment的FragmentActiivity。这个FragementActivity的onActivityResult函数必须调用super(requestCode,resultCode, data); 。super.onActiivtyResult会将未被处理的result消息继续传给其fragement的onActiivtyResult。 
如果这种方法不管用用以下这种：解决方法就是在Fragment中直接调用startActivityForResult()方法，而不是调用 getActivity().startActivityForResult()。