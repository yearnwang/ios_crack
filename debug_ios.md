## 对现在debugserver进行签名

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/ PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>platform-application</key>
	<true/>
	<key>com.apple.springboard.debugapplications</key>
	<true/>
	<key>run-unsigned-code</key>
	<true/>
	<key>get-task-allow</key>
	<true/>
	<key>task_for_pid-allow</key>
	<true/>
</dict>
</plist>

```

必须要有platform-application这个，要不然会显示killed错误。

把上面的文件保存为ent.xml,用以下2个方法进行签名：
codesign -s - --entitlements entitlements.plist -f debugserver
ldid -Sent.xml debugserver






## 调试其他进程的程序

ios11版本以后，越狱后动态调其他程序，会发现
```
    debugserver-@(#)PROGRAM:debugserver PROJECT:debugserver-360.0.26.14
    for arm64.
    Attaching to process Preferences…
    error: failed to attach to process named: “” (os/kern) invalid argument
```

其原因是因为权限不够，需要使用jailbreak_client提权

使用theos的tool模版制作一个小的命令行工具，我这里用的名字是JBDO。make package install 后ssh进入设备
```

#include <spawn.h>
extern char **environ;

void run_cmd(const char *cmd)
{
        pid_t pid;
        const char *argv[] = {"sh", "-c", cmd, NULL};
        int status;
        status = posix_spawn(&pid, "/bin/sh", NULL, NULL, (char* const*)argv, environ);
        if (status == 0)
        {
                if (waitpid(pid, &status, 0) == -1)
                {
                        perror("waitpid");
                }
        }

        printf("status:%d",status);
}


int  main(int argc, char *argv[], char *envp[])
{
    if (argc < 2)
    {
   fprintf(stderr, "usage: %s program args...\n", argv[0]);

       return EXIT_FAILURE;
    }

    int ret, status;
    pid_t pid;
    posix_spawnattr_t attr;

    posix_spawnattr_init(&attr);
    posix_spawnattr_setflags(&attr, POSIX_SPAWN_START_SUSPENDED);

    ret = posix_spawnp(&pid, argv[1], NULL, &attr, &argv[1], envp);

    posix_spawnattr_destroy(&attr);

    if (ret != 0)
    {
        printf("posix_spawnp failed with %d: %s\n", ret, strerror(ret));
        return ret;
    }

    char buf[200];

    snprintf(buf, sizeof(buf), "/electra/jailbreakd_client %d 1", pid);
    printf("execute:%s",buf);
    //system(buf);
    run_cmd(buf);
    //popen(buf,"r");

    kill(pid, SIGCONT);
    waitpid(pid, &status, 0);

    return 0;
}
```

执行命令JBDO debugserver *:1234 -a “Preferences”，发现可以调试了。

process connect connect://localhost:监听端口
