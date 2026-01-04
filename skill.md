---
name: file-to-pdf-converter
description: 将多种文件格式（ZIP、RAR、7Z压缩包和JPG、PNG图片）转换为PDF。支持单文件转换、批量处理以及将多张图片合并为一个PDF。压缩包会被解压并将其中的图片转换为PDF。
---

# 文件转PDF转换器

本技能提供全面的文件转PDF功能。当用户需要将图片或压缩包转换为PDF格式时，使用此技能高效完成转换。

## 支持的格式

### 图片
- **JPG/JPEG** - 标准JPEG图片
- **PNG** - PNG图片（支持透明度）

### 压缩包
- **ZIP** - 标准ZIP压缩包
- **RAR** - RAR压缩包（需要unrar或rarfile）
- **7Z** - 7-Zip压缩包（需要7z或py7zr）

> 压缩包会被解压，其中所有图片会被转换为单个PDF。

## 使用方法

### 快速转换
```python
from file_to_pdf_converter import convert_to_pdf

# 转换单个文件
result = convert_to_pdf('/sdcard/Download/photo.jpg')
print(result.output_path)  # /sdcard/Download/photo.pdf
```

### 使用转换器类
```python
from file_to_pdf_converter import FileToPDFConverter

# 使用选项初始化
converter = FileToPDFConverter(
    output_dir='/sdcard/PDFs',
    fit_to_page=True
)

# 将压缩包转换为PDF
result = converter.convert('/sdcard/Download/comics.zip')

# 检查结果
if result.success:
    print(f"已创建: {result.output_path}")
else:
    print(f"错误: {result.error_message}")
```

### 批量转换
```python
# 转换多个文件
results = converter.convert_batch([
    '/sdcard/img1.jpg',
    '/sdcard/img2.png',
    '/sdcard/archive.zip'
])

# 将多张图片合并为单个PDF
results = converter.convert_batch(
    ['/sdcard/page1.jpg', '/sdcard/page2.jpg'],
    combine=True,
    combined_output_path='/sdcard/combined.pdf'
)
```

### 目录转换
```python
# 转换目录中所有支持的文件
results = converter.convert_directory(
    '/sdcard/Images',
    output_dir='/sdcard/PDFs',
    combine_images=True  # 将所有图片合并为一个PDF
)
```

## 示例

- "把这个JPG转成PDF" → 使用 `convert_to_pdf('/path/to/image.jpg')`
- "把这个ZIP里的图片转成PDF" → 使用 `converter.convert('/path/to/archive.zip')`
- "把这些照片合并成一个PDF" → 使用 `convert_batch([...], combine=True)`
- "把Download文件夹里所有图片转成PDF" → 使用 `convert_directory('/sdcard/Download')`

## 使用指南

1. **始终检查 result.success** 后再使用 output_path
2. **压缩包只提取图片** - 压缩包中的非图片文件会被忽略
3. **图片居中显示** 在A4页面上，默认进行适当缩放
4. **临时文件自动清理** 压缩包处理后自动清理
5. **RAR/7Z支持** 需确保 `unrar`/`7z` 命令可用，或安装可选的Python包

## 模块结构

```
file_to_pdf_converter/
├── __init__.py          # 包导出
├── converter.py         # 主 FileToPDFConverter 类
├── requirements.txt     # 依赖项
├── converters/
│   ├── __init__.py
│   ├── base_converter.py    # 抽象基类
│   ├── image_converter.py   # JPG/PNG 转换器
│   └── archive_converter.py # ZIP/RAR/7Z 转换器
└── utils/
    ├── __init__.py
    ├── file_utils.py    # 文件操作
    └── pdf_utils.py     # PDF生成
```

## 依赖项

- **Pillow** - 图片处理
- **reportlab** - PDF生成
- **PyPDF2** - PDF合并
- **rarfile** (可选) - RAR支持
- **py7zr** (可选) - 7Z支持

## API参考

### ConversionResult 转换结果
```python
@dataclass
class ConversionResult:
    success: bool              # 是否成功
    output_path: Optional[str] # 输出路径
    error_message: Optional[str] # 错误信息
    metadata: Optional[Dict]   # 元数据
```

### FileToPDFConverter 选项
| 选项 | 类型 | 默认值 | 描述 |
|------|------|--------|------|
| output_dir | str | None | 默认输出目录 |
| page_size | tuple | A4 | PDF页面大小 |
| margin | int | 36 | 页边距（点） |
| fit_to_page | bool | True | 缩放图片以适应页面 |
