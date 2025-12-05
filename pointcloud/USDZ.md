Apple在WWDC Conference 2018上宣布,它开发了一种新的文件格式，可以和Pixar公司的USD格式一起工作，叫USDZ。这个格式专为开发者创建AR的3D模型而出。
# 什么是USDZ
A file with .usdz is an uncompressed and unencrypetd ZIP archive for the USD (Universal Scene Description) file format that contains and proxies for files of other formats (such as textures, and animations) embedded within the archive and runs them directly with the USD run-time without any need of unpacking.
# 为什么需要一个新的文件格式
USD - Universal Scene Description, 是Pixar开发的，通过把很多资源文件聚合在一起来创建3D场景。它在各方面都很出色，只是在传输或交付文件时，会带来麻烦，因为有太多的资源文件需要协同组织。于是Pixar和Apple合力开发了以.usdz为扩展的新文件格式。它由许多对象文件组成，但仅被打包成一个对象文件。
# 如何创建USDZ文件

# USDZ示例文件下载
[示例文件](https://docs.fileformat.com/3d/sample.usdz)

# Layout
The only absolute layout requirement a usdz package makes of files within the package is that the data for each file begin at a multiple of 64 bytes from the beginning of the package.