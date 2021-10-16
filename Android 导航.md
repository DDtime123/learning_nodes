# Android 获取位置信息

**大纲：**

* 判断GPS或网络服务是否开启，且引导用户开启服务
* 监听位置信息



#### 1. 判断GPS或网络服务是否开启，且引导用户开启服务

~~~java
    /**
     * @Usage 判断是否开启 GPS 或网络定位开关
     * @return boolean
     */
    private boolean isLocationProviderEnabled(){
        boolean result = false;
        LocationManager locationManager = (LocationManager)this.getApplicationContext().getSystemService(LOCATION_SERVICE);
        if(locationManager == null)
            return false;
        if(locationManager.isProviderEnabled(LocationManager.GPS_PROVIDER) || locationManager.isProviderEnabled(LocationManager.NETWORK_PROVIDER))
            result = true;
        return result;
    }

    /**
     * @Usage 跳转到设置界面，引导用户开启定位服务
     */
    private void openLocationServer(){
        startActivityForResult(new Intent().setAction(Settings.ACTION_LOCALE_SETTINGS),REQUEST_CODE_LOCATION_SERVER);
    }
~~~



#### 2. 监听位置信息

~~~java

~~~