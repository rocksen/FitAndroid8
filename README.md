# FitAndroid8

一行代码完成Android 7 FileProvider适配 以及 Android 8的设配~

适配FileProvide需要声明provider，编写xml，以及在代码中做版本适配等...

可以抽取一个小库简化这些重复性操作，避免重复声明provider，编写xml，以及在代码中做版本适配...

此项目根据 hongyangAndroid的[FitAndroid7](https://github.com/hongyangAndroid/FitAndroid7) 修改,兼容Android 8的apk安装

## 使用


```
allprojects {
		repositories {
			...
			maven { url 'https://jitpack.io' }
		}
	}
```


```
	dependencies {
	        implementation 'com.github.steven2947:FitAndroid8:1.0.0'
	}
```

### 示例一 拍照

```java
private static final int REQUEST_CODE_TAKE_PHOTO = 0x110;
private String mCurrentPhotoPath;

public void takePhotoNoCompress(View view) {
    Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
    if (takePictureIntent.resolveActivity(getPackageManager()) != null) {
        String filename = new SimpleDateFormat("yyyyMMdd-HHmmss", Locale.CHINA)
                .format(new Date()) + ".png";
        File file = new File(Environment.getExternalStorageDirectory(), filename);
        mCurrentPhotoPath = file.getAbsolutePath();
	     // 仅需改变这一行
        Uri fileUri = FileProvider8.getUriForFile(this, file);

        takePictureIntent.putExtra(MediaStore.EXTRA_OUTPUT, fileUri);
        startActivityForResult(takePictureIntent, REQUEST_CODE_TAKE_PHOTO);
    }
}

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (resultCode == RESULT_OK && requestCode == REQUEST_CODE_TAKE_PHOTO) {
        mIvPhoto.setImageBitmap(BitmapFactory.decodeFile(mCurrentPhotoPath));
    }
    // else tip?

}
```

### 示例二 安装apk

```java
public void installApk(View view) {
    File file = new File(Environment.getExternalStorageDirectory(),
            "testandroid8-debug.apk");
    Intent intent = new Intent(Intent.ACTION_VIEW);
    // 仅需改变这一行
    FileProvider8.setIntentDataAndType(this,
            intent, "application/vnd.android.package-archive", file, true);
    startActivity(intent);
}
```


