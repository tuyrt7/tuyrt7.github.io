---
title: 预制视频音乐到sd卡
date: 2019-09-09 08:04:16
tags:
	- blog
	- 定制rom
	- music

---


Rom定制，最常见的就是内置客户提供的视频音乐等文件了，并且要求删除后恢复出厂设置资源还在，对于此类问题，貌似MTK也提供了方法，不过mtk给的方法，代码不好管控   

我们可以把资源随Rom放到手机里，再拷到storage目录里  

<!--more--> 


- 在`system/media/resource`放个`Android.mk`文件，用于拷贝到`out`的`system/media`（此路径可以通过获取 Environment.getRootDirectory().getPath() -> /system ）
每次开机完成的时候，`MediaProvider的MediaScannerReceiver.Java` 都会去接收开机完成的广播，我们可以在这里面进行文件拷贝  

`packages/providers/MediaProvider/src/com/android/providers/media/MediaScannerReceiver.java`   

```  

@Override
public void onReceive(Context context, Intent intent) {
     sharedPreferences = context.getSharedPreferences("first_boot",Context.MODE_PRIVATE);
	 //如果是第一次开机，则sharedPreferences 的值为空，赋值true
     boolean first_boot= sharedPreferences.getBoolean("first_boot", true);
     if (first_boot) {
        Intent activityIntent = new Intent();
        activityIntent.setClassName("com.android.providers.media", "com.android.providers.media.CopyFileService");
		//启动拷贝文件的Services，由于文件比较大，
		//onReceive方法里不能做耗时操作，可以用service解决
        context.startService(activityIntent);
    }
     SharedPreferences.Editor editor = sharedPreferences.edit();
     editor.putBoolean("first_boot", false);//赋值false，表示下次开机不用再拷贝了
     editor.commit();
} 

```  
	
至于`CopyFileService.java`代码，就是一个文件拷贝过程，从`/system/media/resource`拷贝到`/storage/sdcard0`的过程  
   
```  

//调用CopyFileService.java的copyFolder()方法copy
private void start() {
Intent activityIntent = new Intent();
activityIntent.setClassName(“com.smile.weathertest”, “com.android.providers.media.CopyFileService”);
startService(activityIntent);
}  

```  

//CopyFileService.java 代码:
  
   
```   

package com.android.providers.media;

public class CopyFileService extends Service {

    private final static String TAG = CopyFileService.class.getSimpleName();

        private static String PRELOAD_SRC = Environment.getRootDirectory()+"/loadmedia";//*** 这个路径是/system/loadmedia
        private static String PRELOAD_DEST = "/storage/sdcard0/preload";

    private CopyThread mCopyThread;

    @Override
    public IBinder onBind(Intent intent) {
        return null;
    }

    @Override
    public void onCreate() {
        super.onCreate();
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        if (mCopyThread == null) {
            mCopyThread = new CopyThread();
            mCopyThread.start();
        }
        return Service.START_REDELIVER_INTENT;
    }


    public class CopyThread extends Thread {
        @Override
        public void run() {
            if (copyFolder(new File(PRELOAD_SRC), new File(PRELOAD_DEST))) {
                Log.e(TAG, "doPreloadMedia success !");
            } else {
                Log.e(TAG, "doPreloadMedia failed 。");
            }
            stopSelf();
        }
    }

    public boolean copyFolder(File srcFile, File destFile) {
        if (!srcFile.isDirectory()) {
            Log.i(TAG, "srcFile no isDirectory :"+srcFile.getName());
            return false;
        }
        if (!srcFile.canRead()) {
            Log.i(TAG, "no canRead :"+srcFile.getName());

        }
        if (!srcFile.canWrite()) {
            Log.i(TAG, "no canWrite :"+srcFile.getName());
        }

        if (!destFile.exists()) {
            boolean r = false;
            synchronized (this) {
                r = destFile.mkdirs();
            }
            Log.i(TAG, "destFile:exists " + r);
        }
        boolean result = true;
        File[] list = srcFile.listFiles();

        if (list == null) {
            Log.i(TAG, "copyFolder: list null =" + srcFile.getName());
            return false;
        }
        for (File f : list) {
            if (f.isDirectory()) {
                result &= copyFolder(f, new File(destFile, f.getName()));
            } else if(f.isFile()){
                result &= copyFile(f, new File(destFile, f.getName()));
            }
        }
        return result;
    }

    public boolean copyFile(File srcFile, File destFile) {
        boolean result = false;
        try {
            InputStream in = new FileInputStream(srcFile);
            try {
                result = copyToFile(in, destFile);
            } finally {
                in.close();
            }
        } catch (IOException e) {
            result = false;
        }
        return result;
    }

    /**
     * Copy data from a source stream to destFile.
     * Return true if succeed, return false if failed.
     */
    public boolean copyToFile(InputStream inputStream, File destFile) {
        try {
				if (destFile.exists()) {
					destFile.delete();
				}
				FileOutputStream out = new FileOutputStream(destFile);
				try {
					byte[] buffer = new byte[4096];
					int bytesRead;
					while ((bytesRead = inputStream.read(buffer)) >= 0) {
						out.write(buffer, 0, bytesRead);
					}
				} finally {
					out.flush();
					try {
						out.getFD().sync();
					} catch (IOException e) {
                }
                out.close();
            }
            return true;
        } catch (IOException e) {
            return false;
        }
    }
}
```  
