Example
===========

Quick Start
-----------
To start using Draco, you could try the example `webgl_loader_draco.html`.
The code shows a simple example of integration of threejs and Draco Javascript or WebAssembly decoder. From the example, you should be able to load an encoded Draco mesh file and visualize it through threejs's fancy 3D tools.

要开始使用Draco，您可以运行一下webgl_loader_draco.html示例。 该代码展示了一个集成threejs和Draco Javascript或WebAssembly解码器的简单示例。 从这个例子中，你应该能够加载一个编码的Draco网格文件，并通过threejs工具将其可视化。

How to run the example code:  如何运行示例代码：

(1) Clone this project to a working directory, e.g. draco/.

将此项目克隆到工作目录，例如draco/

(2) From the project's root directory, start a local http server.
E.g, with Python, you could run "python -m SimpleHTTPServer".

从项目的根目录中，启动一个本地http服务器。例如，用Python，你可以运行“python -m SimpleHTTPServer”

(3) Load javascript/example/webgl_loader_draco.html. You should be able to see
a bunny rendered by threejs using Draco loader.

运行JavaScript / example / webgl_loader_draco.html。你应该能够看到一个使用Draco loader由threejs渲染的兔子

Advanced Example  高级示例
---------------------
To use more advanced features of Draco loader, please look at the example `webgl_loader_draco_advanced.html`.
In this example, you could:

(1) Switch between Javascript decoder and WebAssembly decoder if it's supported by the browser.

(2) Load your own encoded Draco file (.drc) by clicking on "Choose File" and select a file.

Static Loading Javascript Decoder
---------------------------------

In the previous examples, `DRACOLoader.js` will dynamically load Javascript decoder or
WASM decoder depending on the support of the browser. To avoid dynamical loading the decoder, e.g. to use with JS bundler like webpack, you could directly include `draco_decoder.js` and set the decoder type to `js`. For example:

Include Javascript decoder:
~~~~~ html
<script src="../draco_decoder.js"></script>
~~~~~

Create DracoLoader by setting the decoder type:
~~~~~ js
// (Optional) Change decoder source directory (defaults to './').
THREE.DRACOLoader.setDecoderPath('./path/to/decoder/');

// (Optional) Use JS decoder (defaults to WebAssembly if supported).
THREE.DRACOLoader.setDecoderConfig({type: 'js'});

// (Optional) Pre-fetch decoder source files (defaults to load on demand).
THREE.DRACOLoader.getDecoderModule();

var dracoLoader = new THREE.DRACOLoader();

dracoLoader.load( 'model.drc', function ( geometry ) {

  scene.add( new THREE.Mesh( geometry ) );

  // (Optional) Release the cached decoder module.
  THREE.DRACOLoader.releaseDecoderModule();

} );
~~~~~
