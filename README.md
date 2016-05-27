# redegit-operate
注册表操作实例
http://blog.sina.com.cn/s/blog_4ac0a0d30100e3vg.html
C语言注册表操作实例
例子1：
#include <stdio.h>
#include <windows.h>
int main(void)
{
char regname[]="Software\\Microsoft\\Windows\\CurrentVersion\\Run";
HKEY hkResult;
int ret=RegOpenKey(HKEY_LOCAL_MACHINE,regname,&hkResult);

ret=RegSetValueEx(hkResult,"hacker",0,REG_EXPAND_SZ,(unsigned char *)"%systemroot%\\hacker.exe",25);
if(ret==0){
printf("success to write run key\n");
RegCloseKey(hkResult);
}
else {
     printf("failed to open regedit.%d\n",ret);
     return 0;
}
char modlepath[256];
char syspath[256];
GetModuleFileName(0,modlepath,256);//取得程序名字
GetSystemDirectory(syspath,256);
ret=CopyFile(modlepath,strcat(syspath,"\\hacker.exe"),1);
if(ret)
{
     printf("%s has been copyed to sys dir %s\n",modlepath,syspath);
}
else printf("%s is exisis",modlepath);
return 0;
}


例子2：
#include <windows.h >
#include <stdio.h >
void    main()
{
   HKEY        hKey;       
   char        SubKeyName[]      =      "SOFTWARE\\Microsoft\\Internet        Explorer\\Security ";       
     char        ValueName[]        =      "BlockXBM ";       
        
     BYTE        ValueData[64];       
     DWORD        Buffer;       
        
     //打开       
     if    (RegOpenKeyEx(HKEY_LOCAL_MACHINE,SubKeyName,0,KEY_ALL_ACCESS,&hKey)        !=        ERROR_SUCCESS)       
     {       
         printf(      "Error:        Regedit        cannot        be        opened! ");       
     }       
     else       
     {       
     //读原来的键值       
     Buffer        =        sizeof        (ValueData);       
     if        (RegQueryValueEx(hKey,ValueName,0,NULL,ValueData,&Buffer)        !=        ERROR_SUCCESS)       
     {       
     //不存在       
     //新建一个值为0的DWORD       
     DWORD        temp        =        0;       
     if        (RegSetValueEx(hKey,ValueName,0,REG_DWORD,(LPBYTE)&temp,sizeof(DWORD))        !=        ERROR_SUCCESS)       
     {       
         printf(    "Error:        Create        Value        failed ");       
     }       
     RegCloseKey(hKey);       
     }       
     else       
     {       
     //存在       
     //改变值       
     DWORD        temp        =        0;       
     if        (RegSetValueEx(hKey,ValueName,0,REG_DWORD,(LPBYTE)&temp,sizeof(DWORD))        !=        ERROR_SUCCESS)       
     {       
     printf(    "Error:        Change        Value        failed ");       
     RegCloseKey(hKey);       
     }       
     }       
     }       
}
 
用C语言操作注册表以及上一篇的WinApi操作注册表之外还可以用MFC提供的 CRegKey 类以及 CWinApp提供的SetRegistryKey函数
SetRegistryKey 这个函数功能是设置MFC程序的注册表访问键，并把读写 ini 文件的成员函数映射到读写注册表。只要调用一下 SetRegistryKey 并指定注册表键值，那么下面6个成员函数，就被映射到进行注册表读取了~

WriteProfileBinary	Writes binary data to an entry in the application's .INI file.
WriteProfileInt	Writes an integer to an entry in the application's .INI file.
WriteProfileString	Writes a string to an entry in the application's .INI file.
GetProfileBinary	Retrieves binary data from an entry in the application's .INI file.
GetProfileInt	Retrieves an integer from an entry in the application's .INI file.
GetProfileString	Retrieves a string from an entry in the application's .INI file.
MSDN上面写上面6个函数是写到INI文件的。所以俺就忽略了其访问注册表的功能。无意中看了其MFC实现才有所了解。

例子如下：
SetRegistryKey(_T("boli's app")); //这里是准备在注册表HKEY_CURRENT_USER\\software 下面生成一个boli's app 分支~为什么说是准备呢？因为如果不调用相关函数，如上面提到的6个函数，它是不会真正读写注册表的。具体本文最最下面的MFC实现摘录。
CString strUserName,strPassword;
WriteProfileString("LogInfo","UserName",strUserName); //向注册表HKEY_CURRENT_USER\\software\\boli's app\\LogInfo\\分支下写入 UserName 字符串行键值~
WriteProfileString("LogInfo","Password",strPassword);//同上~

strUserName = GetProfileString("LogInfo","UserName");// 这里是读取HKEY_CURRENT_USER\\software\\boli's app\\LogInfo\\分支下的 UserName 字符串键值到 strUserName~
strPassword = GetProfileString("LogInfo","Password");

如果不是在CWinApp 派生的类中读写注册表，可以直接用：
strUserName = theApp.GetProfileString("LogInfo","UserName");
strPassword = theApp.GetProfileString("LogInfo","Password");
或
strUserName = AfxGetApp()->GetProfileString("LogInfo","UserName");
 
