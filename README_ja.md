# MonoGameNativeAOTTest

このプロジェクトは、Windows-x64上のMonoGameでNativeAOTを使用するサンプルプロジェクトです。実際にNative実行ファイルを出力するのに苦労したため、ここにメモを記しておきます。
とはいえ、現代のPCの実行速度であれば、通常のJITで十分でしょう。

C#で開発された実行ファイルは、逆コンパイルでかなり高い精度で出力できてしまうため、逆コンパイルからの盗用が心配されますが、ソフトが大変ヒットしない限り、その可能性は低いでしょう。それよりも、自分の未熟なプログラムが見られることが恥ずかしいです。そんな訳で、MonoGameでNativeAOTを使用する方法を探し求める旅に出かけました。

使用例を示すウェブページはいくつか見つかりましたが、現状のMonoGame 3.8.1.303では、dotnet publishの際にNativeファイルが出力されず、困りました。

このWebページを見つけたことで、解決できました。
[https://github.com/MonoGame/MonoGame/issues/8005](https://github.com/MonoGame/MonoGame/issues/8005)

解決のカギは、

- .NET7.0でビルドすること
- テンプレートは、DesktopGLであること

でした。MonoGameは.NET6.0対応なので、7.0でビルドしてはいけないと思い込んでいました。

.csproj ファイルのオプションを下記のように追加／変更します。

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

**Microsoft.DotNet.ILCompiler** パッケージのバージョン指定は、**Version="8.0.0-*"** とする必要がありました。.NET8.0はまだプレビュー版なので、使わない方がいいだろうと思い込んでしまっていました。

また、nuget.config は下記の内容です。

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

また、リフレクション対応のために、**rd.xml** というファイルを用意します。自分のプログラムの内容に応じて、適切に追記する必要があるようですが、細かい点はまだ理解できていません。

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

以上の設定で、コマンドラインから  dotnet publish を行えば、.\Publish フォルダ下にNativeファイルが出力されます。

```bash
dotnet publish ./YourProject.csproj --self-contained -r win-x64 -c Release -o .\Publish
```

この文章が、同じようにハマった他の人の役に立てば幸いです。

By Pirota Pirozou
