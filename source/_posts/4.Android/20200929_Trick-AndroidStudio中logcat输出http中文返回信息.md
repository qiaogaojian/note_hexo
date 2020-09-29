# AndroidStudio中logcat输出http中文返回信息

项目中使用retrofit网络框架,但是发现服务器返回的信息logcat打印出来中文部分是unicode码,就写了个转码工具,解决了乱码问题

``` java
    okHttpClient = new OkHttpClient.Builder()
    .addInterceptor(new HttpLoggingInterceptor(new HttpLoggingInterceptor.Logger()
    {
        @Override
        public void log(String message)
        {
            Log.d("Sever", "OkHttp: " + unicodeToUTF_8(message));
        }
    }).setLevel(HttpLoggingInterceptor.Level.BODY))
    .addInterceptor(LoginInterceptor)
    .addNetworkInterceptor(LoginInterceptor)
    .build();

    public static String unicodeToUTF_8(String src)
    {
        if (null == src)
        {
            return null;
        }
        StringBuilder out = new StringBuilder();
        for (int i = 0; i < src.length(); )
        {
            char c = src.charAt(i);
            if (i + 6 < src.length() && c == '\\' && src.charAt(i + 1) == 'u')
            {
                String hex = src.substring(i + 2, i + 6);
                try
                {
                    out.append((char) Integer.parseInt(hex, 16));
                } catch (NumberFormatException nfe)
                {
                    nfe.fillInStackTrace();
                }
                i = i + 6;
            } else
            {
                out.append(src.charAt(i));
                ++i;
            }
        }
        return out.toString();
    }
```