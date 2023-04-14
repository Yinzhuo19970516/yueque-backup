# day35 jq
## 作业

1. 如何把 JSON 转化为 JSONL

jq '.[]' demo.json

2. 如何把 JSONL 转化为 JSON

jq -s '.' demo.jsonl

3. 如何使 JSONL 只输出特定的字段  
   jq '.name' demo.jsonl  
   jq '{name}' demo.jsonl
4. 如何筛选 JSONL 特定字段进行输出  
   cat demo.jsonl | jq -s '. | sort_by(-.age) | .[] | {name, age}'
5. 如何与 less/tail 结合使用  
    jq '.' demo.json | head -7  
   jq '.' demo.json | tail -8

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/wdk8at