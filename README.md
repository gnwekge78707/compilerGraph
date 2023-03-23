# compilerGraph
### 1.1 功能结构和流程图

```mermaid
graph LR
c1[[source code]]
c1==>a1
subgraph frontend
a1([tokenizer])
a1==>c2
c2[[token flow]]
c2==>a2
a2([parser])
a2==>c3
c3[[AST]]

end
a2==>c4

c4[[symbol table]]
c4==>a3
c3==>a3

subgraph midend

subgraph LLVM_midend
a3([LLVM Builer])
b1[Module]
b2[Value]
b3[User]
b4[basicBlock, Function, ...]
end
b1==>a5
a5==>b1
a5([llvm opt pass])
end

b1==>c5
c5[[LLVM Code]]
c5==>a6
subgraph backend
a6([regAlloc])==>
a7([mips opt pass])

end
a7==>c6
c6[[Mips Code]]

style frontend fill: #C6DFF8FF, stroke: #333, stroke-width: 4px;
style backend fill: #C6DFF8FF, stroke: #333, stroke-width: 4px;
style midend fill: #C6DFF8FF, stroke: #333, stroke-width: 4px;
style LLVM_midend fill: #71CED77E, stroke: #333, stroke-width: 4px;
c1:::aa
c2:::aa
c3:::aa
c4:::aa
c5:::aa
c6:::aa



classDef aa fill: #DAAEF8FF, stroke: #333, stroke-width: 4px;
classDef bb fill: #f9a, stroke: #333, stroke-width: 4px;
```

可见，本编译系统主要分为前中后端：

- 前端为词法分析和语法分析，能够从源文件生成**抽象语法树**表示，并且进行一定的语义分析和错误处理。
- **中端为LLVM IR**表示，按照llvm编译器的架构，分为Value，Module，User，Use等抽象结构。前端和中端会合作将ast转化为LLVM IR。并且中端将进行一些优化，最主要的包括支配分析，别名分析，Mem2Reg，全局值标号（相当于传播合并+公共子表达式删除），phi结点删除，死代码删除，分支优化，活跃变量分析，冲突图构建等。
- 冲突图构建后，后端将进行寄存器分配。后端输出**Mips汇编码**，管理一个LLVMIR到MIPS的生成模板、mips isa指令、以及mips的寄存器。



