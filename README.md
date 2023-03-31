# MonoGameNativeAOTTest

This project is a sample project using NativeAOT with MonoGame on Windows-x64. I will make a note here because I had a hard time outputting the actual Native executable file. However, with the execution speed of modern PCs, the normal JIT should be sufficient.

Executable files developed in C# can be output with fairly high accuracy through reverse engineering, so there is a concern about theft from reverse engineering. However, unless the software becomes a big hit, the possibility is low. Rather, it is embarrassing to have your own immature program seen. That's why I embarked on a journey to find out how to use NativeAOT with MonoGame.

I found some web pages showing usage examples, but with the current MonoGame 3.8.1.303, I had trouble outputting the Native file when using "dotnet publish".

I was able to solve the problem by finding this web page.
[https://github.com/MonoGame/MonoGame/issues/8005](https://github.com/MonoGame/MonoGame/issues/8005)

The key to the solution was:

- Building with .NET 7.0
- Using the DesktopGL template

Since MonoGame is compatible with .NET 6.0, I had assumed that I should not build with version 7.0.

To add/modify options in the .csproj file, follow the example below.

```XML
<PropertyGroup>
  <TargetFramework>net7.0</TargetFramework>
  <PublishAot>true</PublishAot>
</PropertyGroup>

<ItemGroup>
  <PackageReference Include="MonoGame.Framework.DesktopGL" Version="3.8.1.303" />
  <PackageReference Include="MonoGame.Content.Builder.Task" Version="3.8.1.303" />
</ItemGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.DotNet.ILCompiler" Version="8.0.0-*" />
  <KnownILCompilerPack Update="Microsoft.DotNet.ILCompiler" TargetFramework="net7.0" ILCompilerPackNamePattern="runtime.win-x64.Microsoft.DotNet.ILCompiler" ILCompilerPackVersion="8.0.0-preview.2.23128.3" ILCompilerRuntimeIdentifiers="win-x64" />
</ItemGroup>
<ItemGroup>
  <RdXmlFile Include="rd.xml" />
</ItemGroup>
```

I needed to specify the version of the **Microsoft.DotNet.ILCompiler** package as **Version="8.0.0-*"**. Since .NET 8.0 is still in preview, I had assumed that it would be better not to use it.

In addition, the contents of nuget.config are as follows:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
 <packageSources>
    <!--To inherit the global NuGet package sources remove the <clear/> line below -->
    <clear />
    <add key="dotnet-public" value="https://pkgs.dev.azure.com/dnceng/public/_packaging/dotnet-public/nuget/v3/index.json" />
    <add key="dotnet-experimental" value="https://pkgs.dev.azure.com/dnceng/public/_packaging/dotnet-experimental/nuget/v3/index.json" />
    <add key="nuget" value="https://api.nuget.org/v3/index.json" />
 </packageSources>
</configuration>
```

In addition, prepare a file called **rd.xml** for reflection support. It seems that you need to append it appropriately depending on the content of your own program, but I haven't fully understood the details yet.

```xml
<Directives xmlns="http://schemas.microsoft.com/netfx/2013/01/metadata">
    <Application>
        <Assembly Name="mscorlib" />
        <Assembly Name="MonoGame.Framework">
            <Type Name="Microsoft.Xna.Framework.Content.ListReader`1[[System.Char,mscorlib]]" Dynamic="Required All" />
            <Type Name="Microsoft.Xna.Framework.Content.ByteReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.SByteReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.DateTimeReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.DecimalReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.BoundingSphereReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.BoundingFrustumReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.RayReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.ListReader`1[[System.Char,System.Private.CoreLib]]" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.ListReader`1[[Microsoft.Xna.Framework.Rectangle,MonoGame.Framework]]" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.ArrayReader`1[[Microsoft.Xna.Framework.Rectangle,MonoGame.Framework]]" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.ListReader`1[[Microsoft.Xna.Framework.Vector3,MonoGame.Framework]]" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.ListReader`1[[Microsoft.Xna.Framework.Content.StringReader,MonoGame.Framework]]" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.ListReader`1[[System.Int32,System.Private.CoreLib]]" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.SpriteFontReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.Texture2DReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.CharReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.RectangleReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.StringReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.Vector2Reader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.Vector3Reader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.Vector4Reader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.CurveReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.IndexBufferReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.BoundingBoxReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.MatrixReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.BasicEffectReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.VertexBufferReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.AlphaTestEffectReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.EnumReader`1[[Microsoft.Xna.Framework.Graphics.SpriteEffects,MonoGame.Framework]]" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.ArrayReader`1[[System.Single,System.Private.CoreLib]]" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.ArrayReader`1[[Microsoft.Xna.Framework.Vector2,MonoGame.Framework]]" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.ListReader`1[[Microsoft.Xna.Framework.Vector2,MonoGame.Framework]]" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.ArrayReader`1[[Microsoft.Xna.Framework.Matrix,MonoGame.Framework]]" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.EnumReader`1[[Microsoft.Xna.Framework.Graphics.Blend,MonoGame.Framework]]" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.NullableReader`1[[Microsoft.Xna.Framework.Rectangle,MonoGame.Framework]]" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.EffectMaterialReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.ExternalReferenceReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.SoundEffectReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.SongReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.ModelReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.Int32Reader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.EffectReader" Dynamic="Required All"/>
            <Type Name="Microsoft.Xna.Framework.Content.SingleReader" Dynamic="Required All"/>
        </Assembly>
    </Application>
</Directives>
```

With the above settings, if you execute dotnet publish from the command line, Native files will be output to the .\Publish folder.

```bash
dotnet publish ./YourProject.csproj --self-contained -r win-x64 -c Release -o .\Publish
```

I hope this article will be useful for other people who may have faced similar difficulties.

By Pirota Pirozou
