内部变量与普通变量：普通变量是指用户自定义的变量，它们的名称和用途可以由用户自己决定；内部变量指综合工具已经定义好的变量，其名称和含义不能改变，用户只可以改变其内容。
set_app_var语句：用于声明内部变量。
set语句：用于声明普通变量。
search_path：内部变量，专门用于提供工具寻找文件的路径，典型用法如下：
```tcl
set_app_var search_path [ concat . \
	RTL路径1 \
	RTL路径2 \
	MEM路径 \
	ROM路径 \
	标准单元库路径 \
	IO单元库路径 \
]
```
说明：MEM、ROM、标准单元、IO单元在Synopsys体系下都使用db文件来描述，该文件是一种二进制文件。

synthetic_library：内部变量，该变量用于装载模型库。   ^synthetic-library
```tcl
set_app_var synthetic_library dw_foundation.sldb
```

target_library：内部变量，仅仅在search_path中提供路径并不能让DC准确地找到该库，因为路径中可能存在多个db文件，工具无法识别哪个才是真正需呀等元器件库，因而需要用户指定。 ^target-library
```tcl
set_app_var target_library [ concat 标准单元库文件名.db \
	IO单元库文件名.db \
	MEM库文件名.db \
	ROM库文件名.db \
]
```