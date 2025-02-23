### PBS 作业调度脚本解析

#### **PBS 作业脚本**

第一步将你的MATLAB程序，包括子程序，上传到一个文件夹。
第二步创建一个txt格式名字为job的文件，将下面五行代码复制到文件中。
第三步将“ jobname11”改名称，这个名称用于在linux识别哪个是你提交的任务。
第四部在终端输入 qnodes ，查看哪个节点可以使用。例如dream2016-22:ppn=22空闲，则修改job文件中对应位置，注意不要节点全用，留两个，dream2016-22有ppn24个，运行22个即可。
第五步将你代码主程序的名字替换掉‘’YpnT2disorder1.m‘’，保存即可。
第六步cd 你存储代码的文件夹路径，进入后 输入 qsub job ，等待几分钟，输入qstat查询任务提交情况，如果提交成功则会显示run。

用于在集群上提交 MATLAB 作业的代码如下：

```bash
#PBS -N jobname11
#PBS -l nodes=dream2016-22:ppn=22
cd $PBS_O_WORKDIR
cat $PBS_NODEFILE
/home/MATLAB/R2019b/bin/matlab -nodesktop -nosplash<  YpnT2disorder1.m > log 2>&1
```

#### **脚本分析**

1. **指定作业名称**：
   ```bash
   #PBS -N jobname11
   ```
   - **含义**：设置作业名称为 `jobname11` ，方便管理和识别作业。
   - **作用**：PBS 调度系统会将此名称用于日志文件和监控作业状态。

2. **请求资源**：
   ```bash
   #PBS -l nodes=1:ppn=24
   ```
   - **含义**：
     - `nodes=1`：请求使用 1 个计算节点。
     - `ppn=24`：请求每个节点使用 24 个处理器核心。
   - **作用**：告诉调度系统作业需要的计算资源，调度系统会根据请求分配选定节点和处理器。

3. **切换工作目录**：
   ```bash
   cd $PBS_O_WORKDIR
   ```
   - **含义**：切换工作目录到作业提交时所在的目录，`$PBS_O_WORKDIR` 是 PBS 提供的环境变量。
   - **作用**：确保脚本运行时的工作环境与提交时一致。

4. **查看节点信息**：
   ```bash
   cat $PBS_NODEFILE
   ```
   - **含义**：打印 PBS 系统生成的节点文件，详细列出分配给作业的节点名称。
   - **作用**：确认作业运行在哪些节点上，或将节点信息传递给其他程序。

5. **运行 MATLAB**：
   ```bash
   /home/MATLAB/R2019b/bin/matlab -nodesktop -nosplash<  YpnT2disorder1.m > log 2>&1
   ```
   - **含义**：
     1. `/home/MATLAB/R2019b/bin/matlab`：运行 MATLAB 的可执行文件。
     2. `-nodesktop`：以无图形用户界面模式运行 MATLAB。
     3. `-nosplash`：启动 MATLAB 时不显示启动画面。
     4. `< YpnT2disorder1.m`：重定向输入，将 MATLAB 脚本 `YpnT2disorder1.m` 的内容作为 MATLAB 的输入。
     5. `> log`：将标准输出重定向到 `log` 文件。
     6. `2>&1`：将标准错误输出也重定向到 `log` 文件中。
   - **作用**：运行 MATLAB 脚本 `YpnT2disorder1.m`，并将运行的日志信息 (包括错误信息) 保存到 `log` 文件中。

---

#### **为什么可以调用节点？**

1. **PBS 作业调度系统**：
   - PBS 是一个分布式作业调度系统，用于在 HPC (高性能计算) 集群中管理和分配计算资源。调用节点是因为脚本使用了 PBS 指令（以 `#PBS` 开头），这些指令会被调度系统解析和执行。

2. **资源分配机制**：
   - PBS 根据脚本中的资源请求（如 `nodes=1:ppn=24`），从集群中找到符合条件的节点，锁定这些资源，并将作业分发到分配的节点上。

3. **节点的角色**：
   - PBS 脚本运行时，请求的节点是集群的一部分，这些节点通常通过高速网络相连。
   - PBS 作业会被提交到调度系统，调度系统会调用定前节点并在其上启动脚本。

