## 提示Operation not permitted

  在iosV11版本以后，我们自己写的程序需要进行签名，如果不签名的话，就会出现以上提示。
  
        iPhone:~ root# /tmp/helloworld 
        -sh: /tmp/helloworld: Operation not permitted
    
  签名步骤：
  1、安装jtool:http://newosxbook.com/tools/jtool.html
  2、生成ent.xml
  
        <?xml version="1.0" encoding="UTF-8"?>
        <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
        <plist version="1.0">
            <dict>
                <key>platform-application</key>
                <true/>
            </dict>
        </plist>
    
  3、签名：
  
      ARCH=arm64 jtool --sign --ent ent.xml helloworld
  
  4、运行：（必须放到/bin文件夹下）
  
        iPhone:~ root# /tmp/helloworld 
        -sh: /tmp/helloworld: Operation not permitted
        iPhone:~ root# mv /tmp/helloworld /bin/
        iPhone:~ root# helloworld
        Hello, world!
    
   5、其他的key类型
  
        <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
        <plist version="1.0">
        <dict>
            <key>com.apple.springboard.debugapplications</key>
            <true/>
            <key>get-task-allow</key>
            <true/>
            <key>proc_info-allow</key>
            <true/>
            <key>task_for_pid-allow</key>
            <true/>
            <key>run-unsigned-code</key>
            <true/>
            <key>platform-application</key>
            <true/>
         </dict>
        </plist>
    
    
    
    参考：
    https://sskaje.me/operation-not-permitted/
