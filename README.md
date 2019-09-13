# MyTextScanning
This project do scanning 


1. Add this library to Gradle file

         implementation 'com.google.android.gms:play-services-vision:17.0.2'

2.  Create activity_main.xml layout (just copy and paste the code)


        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
         xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
         tools:context="com.example.user.mytextscanning.MainActivity">

          <SurfaceView
        android:id="@+id/surfaceView"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1" />

         <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="60dp"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="-20dp"
        android:layout_marginRight="10dp">

        <Button
            android:id="@+id/btnStartScan"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:background="#DCDCDC"
            android:gravity="center"
            android:text="StartScan()"
            android:textColor="#000000"
            android:textSize="14sp" />

        <Button
            android:id="@+id/btnGetText"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:background="#000000"
            android:gravity="center"
            android:text="getText()"
            android:textColor="#ffffff"
            android:textSize="14sp" />

         </LinearLayout>

         <ScrollView
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_margin="10dp"
        android:layout_weight="1"
        android:scrollbars="none">

        <TextView
            android:id="@+id/text_view"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:gravity="left|center"
            android:hint="Description"
            android:textColor="@android:color/black"
            android:textSize="20sp"
            android:textStyle="bold" />
           </ScrollView>

        </LinearLayout>

3.  Create MainActivity.java to create all the function.

   3.1 Call **initiateCameraScan()** function in OnCreate() method.

    public void initiateCameraScan() {
        //Create the TextRecognizer
        final TextRecognizer textRecognizer = new TextRecognizer.Builder(getApplicationContext()).build();
        if (!textRecognizer.isOperational()) {
            Log.w("check", "Detector dependencies not loaded yet");
        } else {

            //Initialize camerasource to use high resolution and set Autofocus on.
            mCameraSource = new CameraSource.Builder(getApplicationContext(), textRecognizer)
                    .setFacing(CameraSource.CAMERA_FACING_BACK)
                    .setRequestedPreviewSize(1280, 1024)
                    .setAutoFocusEnabled(true)
                    .setRequestedFps(2.0f)
                    .build();

            /**
             * Add call back to SurfaceView and check if camera permission is granted.
             *  If permission is granted we can start our cameraSource and pass it to SurfaceView
             */
            mCameraView.getHolder().addCallback(new SurfaceHolder.Callback() {
                @Override
                public void surfaceCreated(SurfaceHolder holder) {
                    try {
                        if (ActivityCompat.checkSelfPermission(getApplicationContext(), Manifest.permission.CAMERA) !=              PackageManager.PERMISSION_GRANTED) {
                            ActivityCompat.requestPermissions(MainActivity.this,
                                    new String[]{Manifest.permission.CAMERA},
                                    requestPermissionID);
                            return;
                        }
                        mCameraSource.start(mCameraView.getHolder());
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }

                @Override
                public void surfaceChanged(SurfaceHolder holder, int format, int width, int height) {

                }

                @Override
                public void surfaceDestroyed(SurfaceHolder holder) {
                    mCameraSource.stop();
                }
            });

            //Set the TextRecognizer's Processor.
            textRecognizer.setProcessor(new Detector.Processor<TextBlock>() {
                @Override
                public void release() {
                }

                /**
                 * Detect all the text from camera using TextBlock and the values into a stringBuilder
                 * which will then be set to the textView.
                 * */
                @Override
                public void receiveDetections(Detector.Detections<TextBlock> detections) {
                    final SparseArray<TextBlock> items = detections.getDetectedItems();
                    if (items.size() != 0) {

                        mTextView.post(new Runnable() {
                            @Override
                            public void run() {
                                StringBuilder stringBuilder = new StringBuilder();
                                for (int i = 0; i < items.size(); i++) {
                                    TextBlock item = items.valueAt(i);
                                    stringBuilder.append(item.getValue());
                                    stringBuilder.append("\n");
                                }
                                mTextView.setText(stringBuilder.toString());
                            }
                        });
                    }
                }
            });
        }
    }

  3.2 Create a Stop button to stop the scanning process.

        mGetText.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                mCameraSource.stop();
            }
        });

  3.3 Create a Start button to Start the scanning process.

      mScanText.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //to recreate the file
                recreate();
            }
        });


**Make sure you check the app/src/main/java/com/example/user/mytextscanning/MainActivity.java to get the complete code and all the variables**

<img src="https://raw.githubusercontent.com/AlhaadiDev/MyTextScanning/master/app/src/main/res/drawable/main_Scan.png"/>
