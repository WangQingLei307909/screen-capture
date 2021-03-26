# screen-capture
iOS 防止系统截屏获取关键信息

------------------------------------------------------------------------------------------------------------------------------------------------------------------
整个过程分成三部分

1、生成展示的二维码MP4视频文件

2、启动webserver

3、播放带DRM加密的hls流

----------------------------------------------------------------------------------------------------------------------------------------------------------------
1、mp4创建

    // _QRCodeImageView UIImageView对象，展示二维码
    WeakSelf;
    [VTMP4Encoder encodeViewToMP4:_QRCodeImageView completion:^(NSData *data) {
        //data 就是视屏流
    }];
2、启动webserver、并播放视频

    //WebServer启动对象
    _webServer = [[GCDWebServer alloc] init];
    NSString *dir=[NSHomeDirectory() stringByAppendingPathComponent:@"tmp/www/"];
    [[NSFileManager defaultManager] createDirectoryAtPath:dir withIntermediateDirectories:YES attributes:nil error:nil];
    // 获取第一步沙盒路径下储存的视频
    NSString *documentsDirectory = [NSHomeDirectory() stringByAppendingPathComponent:@"tmp"];
    NSString *path = [documentsDirectory stringByAppendingPathComponent:@"test.mp4"];
    NSString *toPath = [dir stringByAppendingPathComponent:@"text"];
    if ([[NSFileManager defaultManager] fileExistsAtPath:toPath]) {
        [[NSFileManager defaultManager] removeItemAtPath:toPath error:nil];
    }
    [[NSFileManager defaultManager] copyItemAtPath:path toPath:toPath error:nil];
    //启动服务
    [_webServer addGETHandlerForBasePath:@"/" directoryPath:dir indexFilename:nil cacheAge:3600 allowRangeRequests:YES];
    [_webServer startWithPort:8989 bonjourName:nil];
    //  创建视频播放器，并播放加密的视频
    _player = [VTSimplePlayer new];
    __weak typeof(self) weakSelf = self;
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(0.25 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{

    });
    
    
