# [pVACtools](https://github.com/griffithlab/pVACtools)
## 创建conda环境并安装pVACtools
[pVACtools 6.0.5 官方入门文档](https://pvactools.readthedocs.io/en/latest/index.html)
* Python必须3.6以上版本


        conda create -n pvactools python=3.9 -y
        conda activate pvactools
        #验证python版本
        python -V
#### 1.Adding genotype sample information to your VCF 添加 GT 基因型信息
* 安装工具
  

        pip install vatools
* 处理SNV

  
        vcf-genotype-annotator \
        /data/renweijie/data/HCC1395/SEQC-truth-variants-for-HCC1395-sample/high-confidence_sSNV_in_HC_regions_v1.2.vcf \
        HCC1395 \ #将样本名设为 HCC1395，在执行 pvacseq run 命令时，参数中的 <sample_name> 必须完全等于 HCC1395
        0/1 \
        -o /data/renweijie/data/HCC1395/HCC1395_sSNV_with_GT.vcf
* 处理indel

  
        vcf-genotype-annotator \
        /data/renweijie/data/HCC1395/SEQC-truth-variants-for-HCC1395-sample/high-confidence_sINDEL_in_HC_regions_v1.2.vcf \
        HCC1395 \
        0/1 \
        -o /data/renweijie/data/HCC1395/HCC1395_sINDEL_with_GT.vcf
#### 2.Annotating your VCF with VEP 使用VEP注释
* 安装VEP
  

        conda install -c bioconda ensembl-vep -y
* 安装 pVACseq 专用插件

  
        # 进入你的主数据目录
        cd /data/renweijie/data
        # 创建 VEP 插件目录和 Cache 数据目录
        mkdir -p vep/plugins
        mkdir -p vep/cache
        mkdir -p output/vcf_prepped
        # 安装 pVACseq 专用插件
        pvacseq install_vep_plugin /data/renweijie/data/vep/plugins
* 下载 VEP Cache (关键数据) 

        #直接下载失败了，先下载到本地再上传到服务器
        vep_install -a cf -s homo_sapiens -y GRCh38 \
        -c /data/renweijie/data/vep/cache \
        --CONVERT
        # 解压缩文件
        # 进入压缩包所在的目录
        cd /data/renweijie/data/vep/cache/
        # 解压文件
        tar -zxvf homo_sapiens_vep_115_GRCh38.tar.gz
* 处理SNV

  
        vep \
        --input_file /data/renweijie/data/HCC1395/SEQC-truth-variants-for-HCC1395-sample/VCF_prepped/HCC1395_sSNV_with_GT.vcf \
        --output_file /data/renweijie/data/HCC1395/SEQC-truth-variants-for-HCC1395-sample/VCF_prepped/HCC1395_sSNV_annotated.vcf \
        --format vcf --vcf \
        --symbol --terms SO --tsl --biotype --hgvs \
        --mane_select --canonical \
        --fasta /data/renweijie/data/GRch38/GRCh38/GRCh38.d1.vd1.fa \
        --cache --dir_cache /data/renweijie/data/vep/cache \
        --dir_plugins /data/renweijie/data/vep/plugins \
        --plugin Wildtype --plugin Frameshift \
        --offline --pick --force_overwrite
* 处理INDEL

  
        vep \
        --input_file /data/renweijie/data/HCC1395/SEQC-truth-variants-for-HCC1395-sample/VCF_prepped/HCC1395_sINDEL_with_GT.vcf \
        --output_file /data/renweijie/data/HCC1395/SEQC-truth-variants-for-HCC1395-sample/VCF_prepped/HCC1395_sINDEL_annotated.vcf \
        --format vcf --vcf \
        --symbol --terms SO --tsl --biotype --hgvs \
        --mane_select --canonical \
        --fasta /data/renweijie/data/GRch38/GRCh38/GRCh38.d1.vd1.fa \
        --cache --dir_cache /data/renweijie/data/vep/cache \
        --dir_plugins /data/renweijie/data/vep/plugins \
        --plugin Wildtype --plugin Frameshift \
        --offline --pick --force_overwrite     
#### 3.安装pvactoosl


        pip install pvactools
#### 4.安装NetMHCpan
* 看pvactools支持的版本


        pvacseq valid_algorithms -s human
        # 已经下载果4.1版本，增加执行权限并添加临时环境变量窗口即可
        chmod +x /data/renweijie/Software/netMHCpan-4.1/netMHCpan
        export PATH=$PATH:/data/renweijie/Software/netMHCpan-4.1
#### 5. 运行pVACseq


        pvacseq run \
        /data/renweijie/data/HCC1395/SEQC-truth-variants-for-HCC1395-sample/VCF_prepped/HCC1395_sSNV_annotated.vcf \
        HCC1395 \
        HLA-A*29:02,HLA-B*08:01,HLA-B*45:01,HLA-C*06:02,HLA-C*07:01 \
        NetMHCpan \
        /data/renweijie/pVACtools/HCC1395_SNV_pvac_results_class_i_rank2 \
        -e1 8,9,10,11 \
        -b 500 \
        --percentile-threshold 2\
        -t 2 \
        --keep-tmp-files

        pvacseq run \
        /data/renweijie/data/HCC1395/SEQC-truth-variants-for-HCC1395-sample/VCF_prepped/HCC1395_sINDEL_annotated.vcf \
        HCC1395 \
        HLA-A*29:02,HLA-B*08:01,HLA-B*45:01,HLA-C*06:02,HLA-C*07:01 \
        NetMHCpan \
        /data/renweijie/pVACtools/HCC1395_INDEL_pvac_results_class_i_rank2 \
        -e1 8,9,10,11 \
        -b 500 \
        --percentile-threshold 2 \
        -t 2 \
        --keep-tmp-files
        

        

        
