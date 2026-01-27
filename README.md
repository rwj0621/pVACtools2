# pVACtools2
## 创建conda环境并安装pVACtoosl
* Python必须3.6以上版本


        conda create -n pvactools python=3.9 -y
        conda activate pvactools
        #验证python版本
        python -V
* Adding genotype sample information to your VCF 添加 GT 基因型信息


        pip install vatools
        # 处理SNV
        vcf-genotype-annotator \
  /data/renweijie/data/HCC1395/SEQC-truth-variants-for-HCC1395-sample/high-confidence_sSNV_in_HC_regions_v1.2.vcf \
       HCC1395 \ #将样本名设为 HCC1395，在执行 pvacseq run 命令时，参数中的 <sample_name> 必须完全等于 HCC1395
       0/1 \
       -o /data/renweijie/data/HCC1395/HCC1395_sSNV_with_GT.vcf
       # 处理indel
       vcf-genotype-annotator \
  /data/renweijie/data/HCC1395/SEQC-truth-variants-for-HCC1395-sample/high-confidence_sINDEL_in_HC_regions_v1.2.vcf \
       HCC1395 \
       0/1 \
       -o /data/renweijie/data/HCC1395/HCC1395_sINDEL_with_GT.vcf
* Annotating your VCF with VEP


        conda install -c bioconda ensembl-vep -y
        # 进入你的主数据目录
        cd /data/renweijie/data
        # 创建 VEP 插件目录和 Cache 数据目录
        mkdir -p vep/plugins
        mkdir -p vep/cache
        mkdir -p output/vcf_prepped
        # 安装 pVACseq 专用插件
        pvacseq install_vep_plugin /data/renweijie/data/vep/plugins
        # 下载 VEP Cache (关键数据)
        vep_install -a cf -s homo_sapiens -y GRCh38 \
        -c /data/renweijie/data/vep/cache \
        --CONVERT
        # 处理SNV
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
      
* 安装NetMHCpan


        #官网打开
* 安装pvactoosl


        pip install pvactools

        

        
