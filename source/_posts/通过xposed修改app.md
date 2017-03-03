---
title: 通过xposed修改app
date: 2017-02-27 21:05:52
tags:
---

### 原因
看到第三方app有些debug日志,想看一下具体内容，一般debug日志都有开关控制

方法
- 修改smalli代码，重新打包
	- 比较繁琐，每个APP都得单独打包
- 通过xposed,修改代码
	- 对于不同app都适用的方法，代码比smalli容易写


### 修改代码

- xposed是不支持修改属性的
	- 幸好是静态的属性，所以用反射修改即可

```java
public class RedClock implements IXposedHookLoadPackage {


    public static final String TAG = "per";

    public void handleLoadPackage(final LoadPackageParam loadPackageParam) throws Throwable {
        if (!loadPackageParam.packageName.equals("com.baidu.searchbox"))
            return;

        Log.d(TAG, "handleLoadPackage baidu");

        findAndHookMethod("android.app.Application", loadPackageParam.classLoader,
                "onCreate", new XC_MethodHook() {
                    @Override
                    protected void afterHookedMethod(MethodHookParam param) throws Throwable {
                        try {
                            //分在第二个包里，找不到
                            ReflectionUtils.setStaticBooleanField(Class.forName("com.baidu.searchbox.ef", true, loadPackageParam.classLoader), "GLOBAL_DEBUG", true);

                        } catch (Exception e) {
                            e.printStackTrace();
                        }
                        try {
                            ReflectionUtils.setStaticBooleanField(Class.forName("com.baidu.performance.c", true, loadPackageParam.classLoader), "DEBUG", true);
                        } catch (Exception e) {
                            e.printStackTrace();
                        }
                        try {
                            Log.d(TAG, "GLOBAL_DEBUG" + ReflectionUtils.getByteField(Class.forName("com.baidu.searchbox.ef",
                                    true, loadPackageParam.classLoader), "GLOBAL_DEBUG"));
                        } catch (ClassNotFoundException e) {
                            e.printStackTrace();
                        }
                    }
                });

//        SearchBox feedContext SpeedStats
    }
}

```

### 效果

```bash
02-12 10:25:23.509 W/SpeedStats(18547): ******SpeedStats*******multidex cost 9
02-12 10:25:23.510 W/SpeedStats(18547): ******SpeedStats*******AppCreate cost 121ms
02-12 10:25:23.511 W/SpeedStats(18547): ******SpeedStats*******taskList size=1, [android.app.ActivityManager$RunningTaskInfo@9fdafbf]
02-12 10:25:23.512 W/SpeedStats(18547): ******SpeedStats*******acquirePackageInfo targetActivity={com.baidu.searchbox/com.baidu.searchbox.SplashActivity},homeActivity=null
02-12 10:25:23.512 W/SpeedStats(18547): ******SpeedStats*******topActivity={com.android.launcher3/com.android.launcher3.Launcher}, name={com.baidu.searchbox/com.baidu.searchbox.SplashActivity}, cost=2
02-12 10:25:23.512 W/SpeedStats(18547): ******SpeedStats*******launch type=0
02-12 10:25:28.191 W/SpeedStats(18547): ******SpeedStats*******detectStartAppFrom hasDetect=true,isStartAppFromLauncher=false
02-12 10:25:28.191 W/SpeedStats(18547): ******SpeedStats*******onBaseActivityCreate******有效统计一次*******isFirstEnterMainActivity=true
02-12 10:25:28.409 W/SpeedStats(18547): ******SpeedStats*************setFeedDataType=0
02-12 10:25:28.791 W/SpeedStats(18547): ******SpeedStats************************统计终点*****************
02-12 10:25:28.793 W/SpeedStats(18547): ******SpeedStats************************UBC start*****************
02-12 10:25:28.793 W/SpeedStats(18547): ******SpeedStats*******************{"type":"coldIndirect","value":"none","page":"allcache","from":"mainline"}*************
02-12 10:25:28.793 W/SpeedStats(18547): ******SpeedStats*******************600*************
02-12 10:25:28.793 W/SpeedStats(18547): ******SpeedStats*******************{"splash_act_end":"3637528142","main_act_start":"3637528190","main_act_end":"3637528207","main_frag_start":"3637528210","main_frag_end":"3637528378","main_act_resume_start":"3637528384","main_act_resume_end":"3637528387","get_home_tab_start":"3637528216","home_feed_create_view_start":"3637528245","home_feed_create_view_end":"3637528373","home_feed_init_start":"3637528274","home_feed_init_end":"3637528365","home_tab_measure_start":"3637528415","home_tab_measure_end":"3637528483","home_header_measure_start":"3637528427","home_header_measure_end":"3637528481","home_feed_tool_resume_start":"3637528379","home_feed_tool_resume_end":"3637528379","pull_refresh_start":"3637528594","pull_refresh_end":"3637528778","home_feed_flow_data_start":"3637528408","home_feed_flow_data_end":"3637528409","home_feed_flow_fetch_start":"-1","first_draw":"3637528555","init_feed_flow_start":"3637528583","init_feed_flow_end":"3637528585"}*************
02-12 10:25:28.794 W/SpeedStats(18547): ******SpeedStats*******************4*************
02-12 10:25:28.794 W/SpeedStats(18547): ******SpeedStats************************UBC end*****************
02-12 10:25:28.794 W/SpeedStats(18547): ******SpeedStats************************统计终点*****************

```