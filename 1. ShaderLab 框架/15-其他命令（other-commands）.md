Pixel Shader的input，DX11里改为了SV_Position，output里，COLOR被SV_Target替代

Sample Texture：

DX9:

float4 col = tex2D(Samp, In.TexCd.xy);

 DX11:

float4 col = texture2d.Sample( g_samLinear, In.TexCd.xy);

DX9:

float4 col = tex2Dlod(Samp, float4(In.TexCd.xy,0,level));

DX11:

float4 col = texture2d.SampleLevel( g_samLinear, In.TexCd.xy,level);

 DX9:

float4 col = tex2Dproj(Samp,screenProj.xyzw);

 DX11:

float4 col = Samp.Sample(g_samLinear, .creenProj.xy / screenProj.w );
