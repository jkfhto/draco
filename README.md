
<p align="center">
<img src="docs/DracoLogo.jpeg" />
</p>

News  最新消息
=======
### Version 1.3.3 release  版本1.3.3发布
* Added ExpertEncoder to the Javascript API    向Javascript API添加了ExpertEncoder
  * Allows developers to set quantization options per attribute id    允许开发人员为每个属性ID设置量化选项
* Bug fixes

### Version 1.3.2 release
* Bug fixes

### Version 1.3.1 release
* Fix issue with multiple attributes when skipping an attribute transform

### Version 1.3.0 release
* Improved kD-tree based point cloud encoding
  * Now applicable to point clouds with any number of attributes
  * Support for all integer attribute types and quantized floating point types
* Improved mesh compression up to 10% (on average ~2%)
  * For meshes, the 1.3.0 bitstream is fully compatible with 1.2.x decoders
* Improved Javascript API
  * Added support for all signed and unsigned integer types
  * Added support for point clouds to our Javascript encoder API
* Added support for integer properties to the PLY decoder
* Bug fixes

### Previous releases  以前的版本
https://github.com/google/draco/releases

Description  描述
===========

Draco is a library for compressing and decompressing 3D geometric [meshes] and
[point clouds]. It is intended to improve the storage and transmission of 3D
graphics.

Draco是一个用于压缩和解压缩3D几何网格和点云的库。它旨在改善3D图形的存储和传输。

Draco was designed and built for compression efficiency and speed. The code
supports compressing points, connectivity information, texture coordinates,
color information, normals, and any other generic attributes associated with
geometry. With Draco, applications using 3D graphics can be significantly
smaller without compromising visual fidelity. For users, this means apps can
now be downloaded faster, 3D graphics in the browser can load quicker, and VR
and AR scenes can now be transmitted with a fraction of the bandwidth and
rendered quickly.

Draco是为提高压缩效率和加载速度而设计和制造的。 支持压缩点，连通性信息，纹理坐标，颜色信息，法线和任何其他与几何关联的通用属性。 借助Draco，使用3D图形的应用程序的大小可以显着缩小，而不会影响视觉保真度。 对于用户而言，这意味着现在应用程序可以更快下载，浏览器中的3D图形可以更快加载，而VR和AR场景现在可以基于小部分带宽进行传输和快速渲染。

Draco is released as C++ source code that can be used to compress 3D graphics
as well as C++ and Javascript decoders for the encoded data.

Draco作为C ++源代码发布，可作为压缩3D图形以及编码数据的C ++和Javascript解码器。


_**Contents**_  内容

  * [Building](#building)  构建
    * [CMake Basics](#cmake-basics)
    * [Mac OS X](#mac-os-x)
    * [Windows](#windows)
    * [CMake Build Configuration](#cmake-build-configuration)
      * [Debugging and Optimization](#debugging-and-optimization)
      * [Googletest Integration](#googletest-integration)
      * [Javascript Encoder/Decoder](#javascript-encoderdecoder)
    * [Android Studio Project Integration](#android-studio-project-integration)
    * [Native Android Builds](#native-android-builds)
  * [Usage](#usage)  用法
    * [Command Line Applications](#command-line-applications)
    * [Encoding Tool](#encoding-tool)
    * [Encoding Point Clouds](#encoding-point-clouds)
    * [Decoding Tool](#decoding-tool)
    * [C++ Decoder API](#c-decoder-api)
    * [Javascript Encoder API](#javascript-encoder-api)
    * [Javascript Decoder API](#javascript-decoder-api)
    * [Javascript Decoder Performance](#javascript-decoder-performance)
    * [Metadata API](#metadata-api)
    * [NPM Package](#npm-package)
    * [three.js Renderer Example](#threejs-renderer-example)
  * [Support](#support)
  * [License](#license)
  * [References](#references)


Building
========
For all platforms, you must first generate the project/make files and then
compile the examples.

对于所有平台，首先必须生成 project/ make文件，然后编译示例

CMake Basics  CMake基础知识
------------

To generate project/make files for the default toolchain on your system, run
`cmake` from a directory where you would like to generate build files, and pass
it the path to your Draco repository.

要为系统上的默认工具链生成 project/make 文件，请从您想要生成构建文件的目录运行cmake，并将路径传递给Draco存储库

~~~~~ bash
$ cmake path/to/draco
~~~~~

On Windows, the above command will produce Visual Studio project files for the
newest Visual Studio detected on the system. On Mac OS X and Linux systems,
the above command will produce a `makefile`.

在Windows上，上述命令将为系统上检测到的最新Visual Studio生成Visual Studio项目文件。在Mac OS X和Linux系统上，上述命令将生成一个makefile

To control what types of projects are generated, add the `-G` parameter to the
`cmake` command. This argument must be followed by the name of a generator.
Running `cmake` with the `--help` argument will list the available
generators for your system.

要控制生成哪些类型的项目，请将-G参数添加到cmake命令。这个参数后面必须跟一个生成器的名字。使用--help参数运行cmake将列出系统可用的生成器

Mac OS X
---------

On Mac OS X, run the following command to generate Xcode projects:

在Mac OS X上，运行以下命令生成Xcode项目

~~~~~ bash
$ cmake path/to/draco -G Xcode
~~~~~

Windows
-------

On a Windows box you would run the following command to generate Visual Studio
2015 projects:

在Windows机器上，您将运行以下命令来生成Visual Studio 2015项目

~~~~~ bash
C:\Users\nobody> cmake path/to/draco -G "Visual Studio 14 2015"
~~~~~

To generate 64-bit Windows Visual Studio 2015 projects:

要生成64位Windows Visual Studio 2015项目，请执行以下操作

~~~~~ bash
C:\Users\nobody> cmake path/to/draco -G "Visual Studio 14 2015 Win64"
~~~~~


CMake Build Configuration
-------------------------

Debugging and Optimization  调试和优化
--------------------------

Unlike Visual Studio and Xcode projects, the build configuration for make
builds is controlled when you run `cmake`. The following examples demonstrate
various build configurations.

与Visual Studio和Xcode项目不同，在运行cmake时make build的构建配置会受到控制。以下示例演示了各种构建配置

Omitting the build type produces makefiles that use release build flags
by default:

~~~~~ bash
$ cmake path/to/draco
~~~~~

A makefile using release (optimized) flags is produced like this:

~~~~~ bash
$ cmake path/to/draco -DCMAKE_BUILD_TYPE=release
~~~~~

A release build with debug info can be produced as well:

~~~~~ bash
$ cmake path/to/draco -DCMAKE_BUILD_TYPE=relwithdebinfo
~~~~~

And your standard debug build will be produced using:

~~~~~ bash
$ cmake path/to/draco -DCMAKE_BUILD_TYPE=debug
~~~~~

To enable the use of sanitizers when the compiler in use supports them, set the
sanitizer type when running CMake:

~~~~~ bash
$ cmake path/to/draco -DSANITIZE=address
~~~~~

Googletest Integration  Googletest整合
----------------------

Draco includes testing support built using Googletest. To enable Googletest unit
test support the ENABLE_TESTS cmake variable must be turned on at cmake
generation time:

Draco包含使用Googletest构建的测试支持。要启用Googletest单元测试支持，必须在cmake生成时启用ENABLE_TESTS cmake变量

~~~~~ bash
$ cmake path/to/draco -DENABLE_TESTS=ON
~~~~~

When cmake is used as shown in the above example the Draco cmake file assumes
that the Googletest source directory is a sibling of the Draco repository. To
change the location to something else use the GTEST_SOURCE_DIR cmake variable:

如上例所示使用cmake时，Draco cmake文件假定Googletest源目录是Draco存储库的同级。要将位置更改为其他位置，请使用GTEST_SOURCE_DIR cmake变量

~~~~~ bash
$ cmake path/to/draco -DENABLE_TESTS=ON -DGTEST_SOURCE_DIR=path/to/googletest
~~~~~

To run the tests just execute `draco_tests` from your toolchain's build output
directory.

要运行测试，只需从工具链的构建输出目录执行draco_tests


Javascript Encoder/Decoder  Javascript编码器/解码器
------------------

The javascript encoder and decoder can be built using the existing cmake build
file by passing the path the Emscripten's cmake toolchain file at cmake
generation time in the CMAKE_TOOLCHAIN_FILE variable.
In addition, the EMSCRIPTEN environment variable must be set to the local path
of the parent directory of the Emscripten tools directory.

JavaScript编码器和解码器可以使用现有的cmake构建文件构建，方法是在cmake生成时将Emscripten的cmake工具链文件的路径传递给CMAKE_TOOLCHAIN_FILE变量。 另外，必须将EMSCRIPTEN环境变量设置为Emscripten工具目录的父目录的本地路径

~~~~~ bash
# Make the path to emscripten available to cmake.
$ export EMSCRIPTEN=/path/to/emscripten/tools/parent

# Emscripten.cmake can be found within your Emscripten installation directory,
# it should be the subdir: cmake/Modules/Platform/Emscripten.cmake
$ cmake path/to/draco -DCMAKE_TOOLCHAIN_FILE=/path/to/Emscripten.cmake

# Build the Javascript encoder and decoder.
$ make
~~~~~

WebAssembly Decoder  WebAssembly解码器
-------------------

The WebAssembly decoder can be built using the existing cmake build file by
passing the path the Emscripten's cmake toolchain file at cmake generation time
in the CMAKE_TOOLCHAIN_FILE variable and enabling the WASM build option.
In addition, the EMSCRIPTEN environment variable must be set to the local path
of the parent directory of the Emscripten tools directory.、

WebAssembly解码器可以使用现有的cmake构建文件构建，方法是在cmake生成时将Emscripten的cmake工具链文件的路径传递到CMAKE_TOOLCHAIN_FILE变量中，并启用WASM构建选项。 另外，必须将EMSCRIPTEN环境变量设置为Emscripten工具目录的父目录的本地路径

Make sure to have the correct version of Emscripten installed for WebAssembly
builds. See https://developer.mozilla.org/en-US/docs/WebAssembly.

确保为WebAssembly构建安装了正确版本的Emscripten

~~~~~ bash
# Make the path to emscripten available to cmake.
$ export EMSCRIPTEN=/path/to/emscripten/tools/parent

# Emscripten.cmake can be found within your Emscripten installation directory,
# it should be the subdir: cmake/Modules/Platform/Emscripten.cmake
$ cmake path/to/draco -DCMAKE_TOOLCHAIN_FILE=/path/to/Emscripten.cmake -DENABLE_WASM=ON

# Build the WebAssembly decoder.
$ make

# Run the Javascript wrapper through Closure.
$ java -jar closure.jar --compilation_level SIMPLE --js draco_decoder.js --js_output_file draco_wasm_wrapper.js

~~~~~

WebAssembly Mesh Only Decoder  WebAssembly  仅限网格解码器
-----------------------------

~~~~~ bash

# cmake command line for mesh only WebAssembly decoder.
$ cmake path/to/draco -DCMAKE_TOOLCHAIN_FILE=/path/to/Emscripten.cmake -DENABLE_WASM=ON -DENABLE_POINT_CLOUD_COMPRESSION=OFF

~~~~~

WebAssembly Point Cloud Only Decoder  WebAssembly点云解码器
-----------------------------

~~~~~ bash

# cmake command line for point cloud only WebAssembly decoder.
$ cmake path/to/draco -DCMAKE_TOOLCHAIN_FILE=/path/to/Emscripten.cmake -DENABLE_WASM=ON -DENABLE_MESH_COMPRESSION=OFF

~~~~~


Android Studio Project Integration
----------------------------------

To include Draco in an existing or new Android Studio project, reference it
from the `cmake` file of an existing native project that has a minimum SDK
version of 18 or higher. The project must support C++11.
To add Draco to your project:

  1. Add the following somewhere within the `CMakeLists.txt` for your project
     before the `add_library()` for your project's native-lib:

     ~~~~~ cmake
     # Note "/path/to/draco" must be changed to the path where you have cloned
     # the Draco sources.

     add_subdirectory(/path/to/draco
                      ${CMAKE_BINARY_DIR}/draco_build)
     include_directories("${CMAKE_BINARY_DIR}" /path/to/draco)
     ~~~~~

  2. Add the library target "draco" to the `target_link_libraries()` call for
     your project's native-lib. The `target_link_libraries()` call for an
     empty activity native project looks like this after the addition of
     Draco:

     ~~~~~ cmake
     target_link_libraries( # Specifies the target library.
                            native-lib

                            # Tells cmake this build depends on libdraco.
                            draco

                            # Links the target library to the log library
                            # included in the NDK.
                            ${log-lib} )
     ~~~~~
  3. Add macro to build.gradle for the features you need:
     ~~~~~ cmake
     android {
         ...
         defaultConfig {
             ...
             externalNativeBuild {
                 cmake {
                     cppFlags "-std=c++11"
                     arguments "-DANDROID_STL=c++_shared"
                 }
             }
         }
         externalNativeBuild {
             cmake {
                 path "CMakeLists.txt"
             }
         }
     }

Native Android Builds
---------------------

It's sometimes useful to build Draco command line tools and run them directly on
Android devices via adb.

~~~~~ bash
# All targets require CMAKE_ANDROID_NDK. It must be set in the environment.
$ export CMAKE_ANDROID_NDK=path/to/ndk

# arm
$ cmake path/to/draco -DCMAKE_TOOLCHAIN_FILE=path/to/draco/cmake/toolchains/armv7-android-ndk-libcpp.cmake
$ make

# arm64
$ cmake path/to/draco -DCMAKE_TOOLCHAIN_FILE=path/to/draco/cmake/toolchains/arm64-android-ndk-libcpp.cmake
$ make

# x86
$ cmake path/to/draco -DCMAKE_TOOLCHAIN_FILE=path/to/draco/cmake/toolchains/x86-android-ndk-libcpp.cmake
$ make

# x86_64
$ cmake path/to/draco -DCMAKE_TOOLCHAIN_FILE=path/to/draco/cmake/toolchains/x86_64-android-ndk-libcpp.cmake
$ make
~~~~~

After building the tools they can be moved to an android device via the use of
`adb push`, and then run within an `adb shell` instance.


Usage
======

Command Line Applications
------------------------

The default target created from the build files will be the `draco_encoder`
and `draco_decoder` command line applications. For both applications, if you
run them without any arguments or `-h`, the applications will output usage and
options.

Encoding Tool
-------------

`draco_encoder` will read OBJ or PLY files as input, and output Draco-encoded
files. We have included Stanford's [Bunny] mesh for testing. The basic command
line looks like this:

~~~~~ bash
./draco_encoder -i testdata/bun_zipper.ply -o out.drc
~~~~~

A value of `0` for the quantization parameter will not perform any quantization
on the specified attribute. Any value other than `0` will quantize the input
values for the specified attribute to that number of bits. For example:

~~~~~ bash
./draco_encoder -i testdata/bun_zipper.ply -o out.drc -qp 14
~~~~~

will quantize the positions to 14 bits (default for the position coordinates).

In general, the more you quantize your attributes the better compression rate
you will get. It is up to your project to decide how much deviation it will
tolerate. In general, most projects can set quantization values of about `14`
without any noticeable difference in quality.

The compression level (`-cl`) parameter turns on/off different compression
features.

~~~~~ bash
./draco_encoder -i testdata/bun_zipper.ply -o out.drc -cl 8
~~~~~

In general, the highest setting, `10`, will have the most compression but
worst decompression speed. `0` will have the least compression, but best
decompression speed. The default setting is `7`.

Encoding Point Clouds
---------------------

You can encode point cloud data with `draco_encoder` by specifying the
`-point_cloud` parameter. If you specify the `-point_cloud` parameter with a
mesh input file, `draco_encoder` will ignore the connectivity data and encode
the positions from the mesh file.

~~~~~ bash
./draco_encoder -point_cloud -i testdata/bun_zipper.ply -o out.drc
~~~~~

This command line will encode the mesh input as a point cloud, even though the
input might not produce compression that is representative of other point
clouds. Specifically, one can expect much better compression rates for larger
and denser point clouds.

Decoding Tool
-------------

`draco_decoder` will read Draco files as input, and output OBJ or PLY files.
The basic command line looks like this:

~~~~~ bash
./draco_decoder -i in.drc -o out.obj
~~~~~

C++ Decoder API
-------------

If you'd like to add decoding to your applications you will need to include
the `draco_dec` library. In order to use the Draco decoder you need to
initialize a `DecoderBuffer` with the compressed data. Then call
`DecodeMeshFromBuffer()` to return a decoded mesh object or call
`DecodePointCloudFromBuffer()` to return a decoded `PointCloud` object. For
example:

~~~~~ cpp
draco::DecoderBuffer buffer;
buffer.Init(data.data(), data.size());

const draco::EncodedGeometryType geom_type =
    draco::GetEncodedGeometryType(&buffer);
if (geom_type == draco::TRIANGULAR_MESH) {
  unique_ptr<draco::Mesh> mesh = draco::DecodeMeshFromBuffer(&buffer);
} else if (geom_type == draco::POINT_CLOUD) {
  unique_ptr<draco::PointCloud> pc = draco::DecodePointCloudFromBuffer(&buffer);
}
~~~~~

Please see [src/draco/mesh/mesh.h](src/draco/mesh/mesh.h) for the full `Mesh` class interface and
[src/draco/point_cloud/point_cloud.h](src/draco/point_cloud/point_cloud.h) for the full `PointCloud` class interface.


Javascript Encoder API  Javascript编码器API
----------------------
The Javascript encoder is located in `javascript/draco_encoder.js`. The encoder
API can be used to compress mesh and point cloud. In order to use the encoder,
you need to first create an instance of `DracoEncoderModule`. Then use this
instance to create `MeshBuilder` and `Encoder` objects. `MeshBuilder` is used
to construct a mesh from geometry data that could be later compressed by
`Encoder`. First create a mesh object using `new encoderModule.Mesh()` . Then,
use `AddFacesToMesh()` to add indices to the mesh and use
`AddFloatAttributeToMesh()` to add attribute data to the mesh, e.g. position,
normal, color and texture coordinates. After a mesh is constructed, you could
then use `EncodeMeshToDracoBuffer()` to compress the mesh. For example:

Javascript编码器位于`javascript/draco_encoder.js`文件中。 编码器API可用于压缩网格和点云。 为了使用编码器，您需要先创建一个DracoEncoderModule的实例。 然后使用这个实例来创建MeshBuilder和Encoder对象。 MeshBuilder用于从几何数据构造网格，之后由编码器进行压缩。 首先使用`new encoderModule.Mesh()`创建一个网格对象。 然后，使用AddFacesToMesh（）将索引添加到网格，并使用AddFloatAttributeToMesh（）将位置，法线，颜色和纹理坐标等属性数据添加到网格中。 网格构建好之后，可以使用EncodeMeshToDracoBuffer（）压缩网格。 例如：

~~~~~ js
const mesh = {
  indices : new Uint32Array(indices),
  vertices : new Float32Array(vertices),
  normals : new Float32Array(normals)
};

const encoderModule = DracoEncoderModule();
const encoder = new encoderModule.Encoder();
const meshBuilder = new encoderModule.MeshBuilder();
const dracoMesh = new encoderModule.Mesh();

const numFaces = mesh.indices.length / 3;
const numPoints = mesh.vertices.length;
meshBuilder.AddFacesToMesh(dracoMesh, numFaces, mesh.indices);

meshBuilder.AddFloatAttributeToMesh(dracoMesh, encoderModule.POSITION,
  numPoints, 3, mesh.vertices);
if (mesh.hasOwnProperty('normals')) {
  meshBuilder.AddFloatAttributeToMesh(
    dracoMesh, encoderModule.NORMAL, numPoints, 3, mesh.normals);
}
if (mesh.hasOwnProperty('colors')) {
  meshBuilder.AddFloatAttributeToMesh(
    dracoMesh, encoderModule.COLOR, numPoints, 3, mesh.colors);
}
if (mesh.hasOwnProperty('texcoords')) {
  meshBuilder.AddFloatAttributeToMesh(
    dracoMesh, encoderModule.TEX_COORD, numPoints, 3, mesh.texcoords);
}

if (method === "edgebreaker") {
  encoder.SetEncodingMethod(encoderModule.MESH_EDGEBREAKER_ENCODING);
} else if (method === "sequential") {
  encoder.SetEncodingMethod(encoderModule.MESH_SEQUENTIAL_ENCODING);
}

const encodedData = new encoderModule.DracoInt8Array();
// Use default encoding setting.
const encodedLen = encoder.EncodeMeshToDracoBuffer(dracoMesh,
                                                   encodedData);
encoderModule.destroy(dracoMesh);
encoderModule.destroy(encoder);
encoderModule.destroy(meshBuilder);

~~~~~
Please see [src/draco/javascript/emscripten/draco_web_encoder.idl](src/draco/javascript/emscripten/draco_web_encoder.idl) for the full API.完整的API查看路径

Javascript Decoder API  Javascript解码器API
----------------------

The Javascript decoder is located in [javascript/draco_decoder.js](javascript/draco_decoder.js). The
Javascript decoder can decode mesh and point cloud. In order to use the
decoder, you must first create an instance of `DracoDecoderModule`. The
instance is then used to create `DecoderBuffer` and `Decoder` objects. Set
the encoded data in the `DecoderBuffer`. Then call `GetEncodedGeometryType()`
to identify the type of geometry, e.g. mesh or point cloud. Then call either
`DecodeBufferToMesh()` or `DecodeBufferToPointCloud()`, which will return
a Mesh object or a point cloud. For example:

Javascript解码器位于`javascript/draco_decoder.js`文件中。 Javascript解码器可以解码网格和点云。 为了使用解码器，你必须首先创建一个DracoDecoderModule的实例。 该实例随后被用来创建DecoderBuffer和Decoder对象。 在DecoderBuffer中设置编码数据。 然后调用GetEncodedGeometryType（）来确定几何体的类型，例如 网格或点云。 然后调用DecodeBufferToMesh（）或DecodeBufferToPointCloud（），它将返回一个Mesh对象或一个点云。 例如：

~~~~~ js
// Create the Draco decoder.
const decoderModule = DracoDecoderModule();
const buffer = new decoderModule.DecoderBuffer();
buffer.Init(byteArray, byteArray.length);

// Create a buffer to hold the encoded data.
const decoder = new decoderModule.Decoder();
const geometryType = decoder.GetEncodedGeometryType(buffer);

// Decode the encoded geometry.
let outputGeometry;
let status;
if (geometryType == decoderModule.TRIANGULAR_MESH) {
  outputGeometry = new decoderModule.Mesh();
  status = decoder.DecodeBufferToMesh(buffer, outputGeometry);
} else {
  outputGeometry = new decoderModule.PointCloud();
  status = decoder.DecodeBufferToPointCloud(buffer, outputGeometry);
}

// You must explicitly delete objects created from the DracoDecoderModule
// or Decoder.
decoderModule.destroy(outputGeometry);
decoderModule.destroy(decoder);
decoderModule.destroy(buffer);
~~~~~

Please see [src/draco/javascript/emscripten/draco_web_decoder.idl](src/draco/javascript/emscripten/draco_web_decoder.idl) for the full API.  完整的API查看路径

Javascript Decoder Performance  Javascript解码器性能
------------------------------

The Javascript decoder is built with dynamic memory. This will let the decoder
work with all of the compressed data. But this option is not the fastest.
Pre-allocating the memory sees about a 2x decoder speed improvement. If you
know all of your project's memory requirements, you can turn on static memory
by changing `Makefile.emcc` and running `make -f Makefile.emcc`.

Javascript解码器是用动态内存构建的。 这将让解码器处理所有的压缩数据。 但是这个选项并不是最快的。 预先分配内存可以使解码器的速度提高2倍左右。 如果您知道项目的所有内存要求，则可以通过更改Makefile.emcc并运行make -f Makefile.emcc来打开静态内存

Metadata API
------------
Starting from v1.0, Draco provides metadata functionality for encoding data
other than geometry. It could be used to encode any custom data along with the
geometry. For example, we can enable metadata functionality to encode the name
of attributes, name of sub-objects and customized information.
For one mesh and point cloud, it can have one top-level geometry metadata class.
The top-level metadata then can have hierarchical metadata. Other than that,
the top-level metadata can have metadata for each attribute which is called
attribute metadata. The attribute metadata should be initialized with the
correspondent attribute id within the mesh. The metadata API is provided both
in C++ and Javascript.
For example, to add metadata in C++:

~~~~~ cpp
draco::PointCloud pc;
// Add metadata for the geometry.
std::unique_ptr<draco::GeometryMetadata> metadata =
  std::unique_ptr<draco::GeometryMetadata>(new draco::GeometryMetadata());
metadata->AddEntryString("description", "This is an example.");
pc.AddMetadata(std::move(metadata));

// Add metadata for attributes.
draco::GeometryAttribute pos_att;
pos_att.Init(draco::GeometryAttribute::POSITION, nullptr, 3,
             draco::DT_FLOAT32, false, 12, 0);
const uint32_t pos_att_id = pc.AddAttribute(pos_att, false, 0);

std::unique_ptr<draco::AttributeMetadata> pos_metadata =
    std::unique_ptr<draco::AttributeMetadata>(
        new draco::AttributeMetadata(pos_att_id));
pos_metadata->AddEntryString("name", "position");

// Directly add attribute metadata to geometry.
// You can do this without explicitly add |GeometryMetadata| to mesh.
pc.AddAttributeMetadata(pos_att_id, std::move(pos_metadata));
~~~~~

To read metadata from a geometry in C++:

~~~~~ cpp
// Get metadata for the geometry.
const draco::GeometryMetadata *pc_metadata = pc.GetMetadata();

// Request metadata for a specific attribute.
const draco::AttributeMetadata *requested_pos_metadata =
  pc.GetAttributeMetadataByStringEntry("name", "position");
~~~~~

Please see [src/draco/metadata](src/draco/metadata) and [src/draco/point_cloud](src/draco/point_cloud) for the full API.

NPM Package
-----------
Draco NPM NodeJS package is located in [javascript/npm/draco3d](javascript/npm/draco3d). Please see the
doc in the folder for detailed usage.

Draco NPM NodeJS软件包位于javascript / npm / draco3d中。请参阅该文件夹中的文档了解详细用法
three.js Renderer Example  three.js渲染器示例
-------------------------

Here's an [example] of a geometric compressed with Draco loaded via a
Javascript decoder using the `three.js` renderer.

Please see the [javascript/example/README.md](javascript/example/README.md) file for more information.

Support
=======

For questions/comments please email <draco-3d-discuss@googlegroups.com>

If you have found an error in this library, please file an issue at
<https://github.com/google/draco/issues>

Patches are encouraged, and may be submitted by forking this project and
submitting a pull request through GitHub. See [CONTRIBUTING] for more detail.

License
=======
Licensed under the Apache License, Version 2.0 (the "License"); you may not
use this file except in compliance with the License. You may obtain a copy of
the License at

<http://www.apache.org/licenses/LICENSE-2.0>

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
License for the specific language governing permissions and limitations under
the License.

References
==========
[example]:https://storage.googleapis.com/demos.webmproject.org/draco/draco_loader_throw.html
[meshes]: https://en.wikipedia.org/wiki/Polygon_mesh
[point clouds]: https://en.wikipedia.org/wiki/Point_cloud
[Bunny]: https://graphics.stanford.edu/data/3Dscanrep/
[CONTRIBUTING]: https://raw.githubusercontent.com/google/draco/master/CONTRIBUTING.md

Bunny model from Stanford's graphic department <https://graphics.stanford.edu/data/3Dscanrep/>
