``````
ffmpeg -i input.mov -vf "split [a], pad=iw*2:ih [b], [a] alphaextract, [b] overlay=w" -y output-lr.mp4
``````

Ffmpeg 使用脚本 将视频导出为左右[AFX透明视频](https://smartprogram.baidu.com/docs/design/resource/animationVideo/)



在xr-Frame中将材质通道更改 createEffect
``````javascript
const xrFrameSystem = wx.getXrFrameSystem();
function createVideoTsbsEffect(scene) {
    return scene.createEffect(
        {
            "name": "video-tsbs",
            "properties": [
                {
                    "key": "u_baseColorFactor",
                    "type": 3,
                    "default": [1, 1, 1, 0]
                }
            ],
            "images": [{ "key": "u_baseColorMap", "default": "white", "macro": "WX_USE_BASECOLORMAP" }],
            "defaultRenderQueue": 3000,
            "passes": [
                {
                    "renderStates": { "cullOn": true, "blendOn": true, "depthWrite": true, "cullFace": 2 },
                    "lightMode": "ForwardBase",
                    "useMaterialRenderStates": true,
                    "shaders": [0, 1]
                }],
            "shaders": [
                `#version 100
  precision highp float;
  attribute vec3 a_position;
  attribute vec2 a_texCoord;


  uniform mat4 u_projection;
  uniform mat4 u_world;
  uniform mat4 u_view;
  varying highp vec2 vTextureCoord;
  
  void main()
  {
    vTextureCoord = a_texCoord;
    gl_Position = u_projection * u_view * u_world * vec4(a_position,1.0);
  }`,
                `#version 100
  precision highp float;


  uniform highp vec4 u_baseColorFactor;
  #ifdef WX_USE_BASECOLORMAP
    uniform sampler2D u_baseColorMap;
  #endif


  varying highp vec2 vTextureCoord;


  void main()
  {
#ifdef WX_USE_BASECOLORMAP
  vec4 color = texture2D(u_baseColorMap, vec2(vTextureCoord.x*0.5,vTextureCoord.y));
  vec4 colora = texture2D(u_baseColorMap, vec2(vTextureCoord.x*0.5 + 0.5,vTextureCoord.y));


    vec4 baseColor = vec4(color.xyz,colora.x);
#else
    vec4 baseColor = u_baseColorFactor;
#endif


  gl_FragData[0] = baseColor;
  }  
    `]
        }
    )
}
xrFrameSystem.registerEffect("video-tsbs", createVideoTsbsEffect);
``````

