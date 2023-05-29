# 如何查看Android Crash文件
1. 在导出Unity包的时候携带Unity的符号表或者可以在Unity的路径下找到对应的libUnity.so文件
> 本地符号表路径：**{Unity路径}\Editor\Data\PlaybackEngines\windowsstandalonesupport\Variations\il2cpp**找到对应的符号表
> Unity打包出来的符号表使用libil2cpp.so.debug文件
2. 使用NDK的**addr2line**命令
> addr2line命令有64位和32位的区别，如果报错中是**arm64** 需要使用64位的命令
> addr2line命令的路径 **{你的NDK路径}\toolchains\aarch64-linux-android-addr2line\prebuilt\windows-x86_64\bin**
>解析命令：addr2line {符号表} {报错的地址}
3. 解析出来的是当前地址对应的方法。

# Android aab格式包规定
 aab格式的包是Google规定的上传Google商店的指定包。
 ## aab包的组成规范
 ```mermaid
 graph TD
    A[aab] -->B(APK) -->大小限制:150mb ---->E
    A -->C(Asset Package)
    C -->D(大小限制:2G,总资源数量<50)
    D -->install-time -->大小限制:1G\nGoogle商店下载包体的时候会一起下载.\n全部下载结束之后才可以进入游戏. -->E
    D -->fast-follow -->大小限制:512MB\n在包体下载完成之后后台进行下载.\n此时可以进入游戏. -->E
    D -->on-demand -->大小限制:512MB\n启动游戏之后才会开始下载 -->E
    E(结束)

 ```