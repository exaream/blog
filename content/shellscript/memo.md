# Shellscript

## Double quotes

### Use double quotes when you want to apply line breaks
```shell
#! /bin/bash

LINES=`cat <<EOF
apple
banana
lemon
EOF
`

echo ${LINES}
# apple banana lemon


echo "${LINES}"
# apple
# banana
# lemon

```


###  Don not use double quotes when using `for` clause
```shell
#! /bin/bash

LINES=`cat <<EOF
apple
banana
lemon
EOF
`


COUNT=0
for i in ${LINES}
do
  echo ${COUNT}':'${i}
  COUNT=$(( COUNT + 1 ))
done
# 0:apple
# 1:banana
# 2:lemon


COUNT=0
for i in "${LINES}"
do
  echo ${COUNT}':'${i}
  COUNT=$(( COUNT + 1 ))
done
# 0:apple banana lemon
```


## References
* [複数行の文字列をShell変数に格納して、forで回した時の挙動](https://qiita.com/taro-hida/items/552aa905d343f7395974)