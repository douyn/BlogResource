## Camera2应用开发

#### 1. 打开相机，开启预览
在布局文件中加入TextureView,然后对其进行监听

	textureview = (TextureView) findViewById(R.id.textureView);

	textureview.setSurfaceTextureListener(surfaceTextureListener);

当Textureview准备好，会回调surfacetexturelistener的onSurfaceAvailabale()，我们在预览之前给相机设置参数，然后打开相机

	TextureView.SurfaceTextureListener textureListener = new TextureView.SurfaceTextureListener() {
    @Override
    public void onSurfaceTextureAvailable(SurfaceTexture surface, int width, int height) {
        //当SurefaceTexture可用的时候，设置相机参数并打开相机
        setupCamera(width, height);
        openCamera();
    }
	};

设置相机参数，主要是根据textureview的尺寸设置预览尺寸。
	
	private void setupCamera(int width, int height) {
        //获取摄像头的管理者CameraManager
    CameraManager manager = (CameraManager) getSystemService(Context.CAMERA_SERVICE);
    try {
        //遍历所有摄像头
        for (String cameraId: manager.getCameraIdList()) {
            CameraCharacteristics characteristics = manager.getCameraCharacteristics(cameraId);
            //默认打开后置摄像头
            if (characteristics.get(CameraCharacteristics.LENS_FACING) == CameraCharacteristics.LENS_FACING_FRONT)
                continue;
            //获取StreamConfigurationMap，它是管理摄像头支持的所有输出格式和尺寸
            StreamConfigurationMap map = characteristics.get(CameraCharacteristics.SCALER_STREAM_CONFIGURATION_MAP);
            //根据TextureView的尺寸设置预览尺寸
            mPreviewSize = getOptimalSize(map.getOutputSizes(SurfaceTexture.class), width, height);
            mCameraId = cameraId;
            break;
        }
    } catch (CameraAccessException e) {
        e.printStackTrace();
    }
	}

打开相机，通过cameramanager打开相机，在状态回调中打开相机预览。

	private void openCamera() {
    //获取摄像头的管理者CameraManager
    CameraManager manager = (CameraManager) getSystemService(Context.CAMERA_SERVICE);
    //检查权限
    try {
        if (ActivityCompat.checkSelfPermission(this, Manifest.permission.CAMERA) != PackageManager.PERMISSION_GRANTED) {
            return;
        }
        //打开相机，第一个参数指示打开哪个摄像头，第二个参数stateCallback为相机的状态回调接口，第三个参数用来确定Callback在哪个线程执行，为null的话就在当前线程执行
        manager.openCamera(mCameraId, stateCallback, null);
    } catch (CameraAccessException e) {
        e.printStackTrace();
    }
	}

	private final CameraDevice.StateCallback stateCallback = new CameraDevice.StateCallback() {
    @Override
    public void onOpened(CameraDevice camera) {
        mCameraDevice = camera;
        //开启预览
        startPreview();
    }
	}

通过textureview显示相机预览数据，camera2的拍照数据和预览数据都是通过cameracapturesession实现的。

	private void startPreview() {
    SurfaceTexture mSurfaceTexture = mTextureView.getSurfaceTexture();
    //设置TextureView的缓冲区大小
    mSurfaceTexture.setDefaultBufferSize(mPreviewSize.getWidth(), mPreviewSize.getHeight());
    //获取Surface显示预览数据
    Surface mSurface = new Surface(mSurfaceTexture);
    try {
        //创建CaptureRequestBuilder，TEMPLATE_PREVIEW比表示预览请求
        mCaptureRequestBuilder = mCameraDevice.createCaptureRequest(CameraDevice.TEMPLATE_PREVIEW);
        //设置Surface作为预览数据的显示界面
        mCaptureRequestBuilder.addTarget(mSurface);
        //创建相机捕获会话，第一个参数是捕获数据的输出Surface列表，第二个参数是CameraCaptureSession的状态回调接口，当它创建好后会回调onConfigured方法，第三个参数用来确定Callback在哪个线程执行，为null的话就在当前线程执行
        mCameraDevice.createCaptureSession(Arrays.asList(mSurface), new CameraCaptureSession.StateCallback() {
            @Override
            public void onConfigured(CameraCaptureSession session) {
                try {
                        //创建捕获请求
                        mCaptureRequest = mCaptureRequestBuilder.build();
                        mPreviewSession = session;
                        //设置反复捕获数据的请求，这样预览界面就会一直有数据显示
                        mPreviewSession.setRepeatingRequest(mCaptureRequest, mSessionCaptureCallback, null);
                    } catch (CameraAccessException e) {
                        e.printStackTrace();
                    }
                }

            @Override
            public void onConfigureFailed(CameraCaptureSession session) {

            }
        }, null);
    } catch (CameraAccessException e) {
        e.printStackTrace();
    }
	}


#### Camera1 流程


 * 添加相机相关权限
 * 通过Camera.open(int)获得一个相机实例
 * 利用camera.getParameters()得到相机实例的默认设置Camera.Parameters
 * 如果需要的话，修改Camera.Parameters并调用camera.setParameters(Camera.Parameters)来修改相机设置
 * 调用camera.setDisplayOrientation(int)来设置正确的预览方向
 * 调用camera.setPreviewDisplay(SurfaceHolder)来设置预览，如果没有这一步，相机是无法开始预览的
 * 调用camera.startPreview()来开启预览，对于拍照，这一步是必须的
 * 在需要的时候调用camera.takePicture(Camera.ShutterCallback, Camera.PictureCallback, Camera.PictureCallback, Camera.PictureCallback)来拍摄照片
 * 拍摄照片后，相机会停止预览，如果需要再次拍摄多张照片，需要再次调用camera.startPreview()来重新开始预览
 * 调用camera.stopPreview()来停止预览
 * 一定要在onPause()的时候调用camera.release()来释放camera，在onResume中重新开始camera


#### 原理

![camera2流程示意图](http://img.blog.csdn.net/20150524141843503)

![camera2中主要类](http://img.blog.csdn.net/20150524142911722)

![拍照流程图](http://img.blog.csdn.net/20150524143457419)
#### 参考
[android.hardware.camera2使用指南](http://blog.csdn.net/lincyang/article/details/45951225)