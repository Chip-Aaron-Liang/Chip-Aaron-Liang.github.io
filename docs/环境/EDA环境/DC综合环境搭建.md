# 变量定义
**内部变量与普通变量**：普通变量是指用户自定义的变量，它们的名称和用途可以由用户自己决定；内部变量指综合工具已经定义好的变量，其名称和含义不能改变，用户只可以改变其内容。
- **`set_app_var`语句**：用于声明内部变量。
- **`set语句`**：用于声明普通变量。

- **`search_path`内部变量**：专门用于提供工具寻找文件的路径，典型用法如下：
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
说明：MEM、ROM、标准单元、IO单元在Synopsys体系下都使用`.db`文件来描述，该文件是一种二进制文件。 ^search-path

- **`synthetic_library`内部变量**：该变量用于装载模型库。   ^synthetic-library
```tcl
set_app_var synthetic_library dw_foundation.sldb
```

- **`target_library`内部变量**：仅仅在`search_path`中提供路径并不能让DC准确地找到该库，因为路径中可能存在多个`.db`文件，工具无法识别哪个才是真正需要的元器件库，因而需要用户指定。 ^target-library
```tcl
set_app_var target_library [ concat 标准单元库文件名.db \
	IO单元库文件名.db \
	MEM库文件名.db \
	ROM库文件名.db \
]
```

- **`link_library`内部变量**：在link步骤中工具会查询link_library ^link-library
```tcl
set_app_var link_library [ concat * $synthetic_library $target_library]
```

- 用户还需要设置综合时存储临时文件的地址（主要是与Synopsys相关的二进制文件），一个是写路径，一个是读路径，分别设置在内部变量`cache_write`和`cache_read`中，一般这两个路径为同一个路径 ^cache-read
```tcl
set_app_var cache_write 路径A1
set_app_var cache_read  路径A1
```

- 用户可以指定服务器中CPU核数：
```tcl
set_host_options -max_cores 8
```

- 可以指定被综合的RTL所使用的语法标准，若未经指定，则默认为Verilog-2001标准
```tcl
set_app_var hdlin_vrlg_std 2005
```

- 用户可以选择对设计中的常数进行优化，例如综合器会删除一些常数，并且用本模块之外的相同常数取而代之，这样可以减少面积，但可能导致形式验证失败，因而保守起见该选项不开启
```tcl
set_app_var compile_seqmap_propagate_constants false
```

- 用户可以允许工具在读取RTL是自动识别其中的时钟门控
```
set_app_var power_cg_auto_identify true
```

- 用户可以设置开关让工具发现锁存器后报警
```tcl
set_app_var hdlin_check_no_latch true
```

- 将综合中elaborate步骤产生的临时文件集中存放在一个路径下
```tcl
define_design_lib work -path 路径A2 
```

# 步骤命令
有关综合流程的详细描述见[[综合基础理论#详细流程]]
- **`analyze`步骤**：`-format verilog`表示使用Verilog的语法进行解析 ^analyze
```tcl
analyze -format verilog $rtl_files
```

- **`elaborate`步骤**：此处的design_top为RTL设计顶层模块名 ^elaborate
```tcl
elaborate design_top
```

- **`compile`步骤**：使用`-gate_clock`自动识别并加入时钟门控，使用`-scan`选择带有scan功能的元器件，这些元器件比不带Scan功能的同类元器件面积更大 ^complie
```tcl
compile_ultra -gate_clock -scan
```

- **输出**：`compile`完成后，需要用户手动输出网表文件及工程文件。综合信息为一个二进制文件，扩展名为ddc。
```tcl
write -hirarchy -format verilog -output design.v
write -hirarchy -format ddc -output design.ddc
```
在下次使用时，可使用`read_ddc`读取design.ddc，即可查看本次综合的信息

- **报告**：`-max_paths`表示每个路径组报告10条路径，如果按照[[DC综合环境搭建#^group-path|group_path示例]]分成四个信号组，那么总共报告40条时序路径。`-delay_type max`表示只报告建立时间的分析结果，若修改为`-delay_type min`则只报告保持时间的分析结果。
```tcl
report_timing -max_path 10 -delay_type max > timing.rpt
report_area -physical -nosplit > area.rpt
report_qor -nosplit > qor.rpt
report_clock_gating -nosplit > clock_gating.rpt
```

# 约束SDC
- **信号时序分组和命名**：`weight`代表权重，即综合时工具的努力程度，默认值为1。`all_registers`\ `all_inputs` \ `all_outputs`都是工具定义的函数，意思分别是选中所有的寄存器、输入端和输出端 ^group-path
```tcl
group_path -name reg2reg -from [all_registers] -to [all_registers] -weight 20
group_path -name in2reg -from [all_inputs] -to [all_registers]
group_path -name reg2out -from [all_registers] -to [all_outputs]
group_path -name in2out -from [all_inputs] -to [all_outputs]
```

# 运行
将预综合准备的变量定义、步骤命令全部写入一个文本文件中，如命名为syn.tcl。运行脚本命令为：
```shell
dc_shell -f syn.tcl | tee dc.log
```