##Custom Shader GUI
Sometimes you have a shader with some interesting data types that can not be nicely represented using the built in Unity material editor. Unity provides a way to override the default way shader properties are presented so that you can define your own. You can use this feature to define custom controls and data range validation.

The first part to writing custom editor for your shader’s gui is defining a shader that requires a Custom Editor. The name you use for the custom editor is the class that will be looked up by Unity for the material editor.

To define a custom editor you extend from the ShaderGUI class and place the script below an Editor folder in the assets directory.

```csharp
using UnityEditor;

public class CustomShaderGUI : ShaderGUI 
{
    public override void OnGUI (MaterialEditor materialEditor, MaterialProperty[] properties)
    {
        base.OnGUI (materialEditor, properties);
    }
}
```

Any shader that has a custom editor defined (CustomEditor “CustomShaderGUI”) will instantiate an instance of the shader gui class listed above and execute the associated code.

####A simple example

So we have a situation where we have a shader that can work in two modes; it renders standard diffuse lighting or it renders the blue and green channels with 50%.

```csharp
Shader "Custom/Redify" {
    Properties {
        _MainTex ("Base (RGB)", 2D) = "white" {}
    }
    SubShader {
        Tags { "RenderType"="Opaque" }
        LOD 200
        
        CGPROGRAM
        #pragma surface surf Lambert addshadow
        #pragma shader_feature REDIFY_ON

        sampler2D _MainTex;

        struct Input {
            float2 uv_MainTex;
        };

        void surf (Input IN, inout SurfaceOutput o) {
            half4 c = tex2D (_MainTex, IN.uv_MainTex);
            o.Albedo = c.rgb;
            o.Alpha = c.a;

            #if REDIFY_ON
            o.Albedo.gb *= 0.5;
            #endif
        }
        ENDCG
    } 
    CustomEditor "CustomShaderGUI"
}
```

As you can see the shader has a Keyword available for setting: REDIFY_ON. This can be changed be set on a per material basis by using the shaderKeywords property of the material. Below is an ShaderGUI instance that does this.

```
using UnityEngine;
using UnityEditor;
using System;

public class CustomShaderGUI : ShaderGUI
{
    public override void OnGUI(MaterialEditor materialEditor, MaterialProperty[] properties)
    {
        // render the default gui
        base.OnGUI(materialEditor, properties);

        Material targetMat = materialEditor.target as Material;

        // see if redify is set, and show a checkbox
        bool redify = Array.IndexOf(targetMat.shaderKeywords, "REDIFY_ON") != -1;
        EditorGUI.BeginChangeCheck();
        redify = EditorGUILayout.Toggle("Redify material", redify);
        if (EditorGUI.EndChangeCheck())
        {
            // enable or disable the keyword based on checkbox
            if (redify)
                targetMat.EnableKeyword("REDIFY_ON");
            else
                targetMat.DisableKeyword("REDIFY_ON");
        }
    }
}
```

For a more comprehensive ShaderGUI example see the StandardShaderGUI.cs file together with the Standard.shader found in the ‘Built-in shaders’ package that can be downloaded from Unity Download Archive.

Note that the simple example above could also be solved much simpler using MaterialPropertyDrawers. Add the following line to the Properties section of the Custom/Redify shader:

```
    [Toggle(REDIFY_ON)] _Redify("Red?", Int) = 0
```

and remove the:

```
    CustomEditor "CustomShaderGUI"
```
