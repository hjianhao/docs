
<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [rm](#rm)
  - [删除触指定文件/目录外其他的文件和目录](#删除触指定文件目录外其他的文件和目录)

<!-- /code_chunk_output -->



# rm

## 删除触指定文件/目录外其他的文件和目录

删除除文件名为filename以外的所有文件或目录
```bash
rm -v !("filename")
```

删除除文件名为filename1和filename2以外的所有文件或目录
```bash
rm -v !("filename1"|"filename2") 
```

删除一个目录下除了所有的.zip 和 .odt 文件的所有文件
```bash
rm -v !(*.zip|*.odt)
```