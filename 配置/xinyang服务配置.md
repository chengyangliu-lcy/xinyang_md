# xinyang服务配置

conda activate material_api

  * 5005端口服务


cd /home/xinyang/sch_design && nohup python read_net_designid_and_components.py > /home/xinyang/sch_design/read_net_designid_and_components.log 2>&1 &

​  


# 后台启动8010端⼝服务 

nohup python api_small_batch_643.py > api_8010.log 2>&1 &

  * 5000服务


d /home/xinyang/sch_design && nohup python read_markdown_api.py > /home/xinyang/sch_design/read_markdown_api.log 2>&1 &

​  


# 或者后台运⾏ 

# nohup uvicorn api_server:app --host 0.0.0.0 --port 5010 > api.log 2>&1 &

​  


  * 5007


cd /home/xinyang/sch_design && nohup python net2svg_api.py > /home/xinyang/sch_design/net2svg_api.log 2>&1 &
