
比赛链接：https://www.datafountain.cn/competitions/424

采用框架：修改transformers框架里面的run_squad.py文件代码，链接 https://github.com/huggingface/transformers/tree/master/examples 直接按要求安装transformers即可。

疫情问答助手baseline思路比较简单：把数据整成官方训练数据json格式训练即可。预测文件难点在于找到答案文档。这里基于bm25检索文档，为了提高答案召回率可以按照bm25得分多取几个，这里比较佛系，我取了个top3预测。线上提交得分0.58+。

数据处理,生成训练数据,预测数据都在data_process.py里面.

这里用的是bert_wwm文件这里可以下载：https://github.com/ymcui/Chinese-BERT-wwm

训练执行：python gf_run_squad.py \
    --model_type bert \
    --model_name_or_path /home/wq/bert_wwm/pytorch_model.bin \
    --config_name /home/wq/bert_wwm/bert_config.json \
    --tokenizer_name /home/wq/bert_wwm/vocab.txt \
    --do_train \
    --do_eval \
    --do_lower_case \
    --train_file /home/wq/train_0.8.json \
    --predict_file /home/wq/valid_0.2.json \
    --per_gpu_train_batch_size 6 \
    --learning_rate 3e-5 \
    --num_train_epochs 4 \
    --max_seq_length 512 \
    --max_query_length 80 \
    --max_answer_length 400 \
    --warmup_steps 2500 \
    --doc_stride 128 \
    --output_dir /home/wq/torch0413
 
 下面checkpoint-3是训练过程生成最优模型所在路径.
 预测执行：python gf_run_squad.py \
    --model_type bert \
    --model_name_or_path "/home/wq/torch0413/checkpoint-3" \
    --do_eval \
    --do_test \
    --do_lower_case \
    --predict_file /home/wq/test_torch_0413_N3_json.json \
    --test_prefix "0413" \
    --learning_rate 3e-5 \
    --max_seq_length 512 \
    --max_query_length 80 \
    --max_answer_length 400 \
    --doc_stride 128 \
    --output_dir "/home/wq/torch0413/checkpoint-3"

这题难点在于不用外部数据情况下,针对预测数据既能准确找到答案所在文档又能使文档尽可能的短。第一次接触问答,欢迎有兴趣的同学多交流。



