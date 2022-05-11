---
title: 使用celerykubernetesexecutor
keywords:
- 使用celerykubernetesexecutor
- mlzhang
description : "使用celerykubernetesexecutor"
---
## 同一DAG使用CeleryExecutor和KubernetesExecutor

- Airflow版本：2.2.5

- 设置环境变量 AIRFLOW_\_CORE__EXECUTOR: "CeleryKubernetesExecutor"

- 使用 KubernetesExecutor 需要设置任务 queue='kubernetes'



示例DAG

```python


from airflow import DAG
from airflow.decorators import task
from airflow.utils.dates import days_ago
from airflow.settings import AIRFLOW_HOME

import os
import time

with DAG(
    dag_id="example_kubernetes_executor",
    schedule_interval="33 * * * *",
    start_date=days_ago(2),
    catchup=False,
    tags=["example"],
) as dag:
    executor_config = {
        "pod_template_file": os.path.join(
            AIRFLOW_HOME, "base.yaml"
        ),
        #"pod_override": k8s.V1Pod(
        #    metadata=k8s.V1ObjectMeta(labels={"release": "stable"})
        #),
    }

    for i in range(2):
        @task(task_id=f'kubernetes_task_{i}', executor_config=executor_config, queue='kubernetes')
        def test11():
            print('------------- start ----------------')
            for i in range(10):
                print(f'{i}' * 10)
                time.sleep(3)
            print('-------------- end ------------------')
        test_task = test11()


    for i in range(2):
        @task(task_id=f'celery_task_{i}')
        def test222():
            print('------------- start ----------------')
            for i in range(10):
                print(f'{i}' * 10)
                time.sleep(3)
            print('-------------- end ------------------')

        test_task2 = test222()
```



Pod_template 文件示例

- base.yaml

```yaml

---
apiVersion: v1
kind: Pod 
metadata:
  name: testname
  namespace: cmbchina
spec:
  containers:
    - name: base
      image: 'aiflow:xxxxx'
      imagePullPolicy: IfNotPresent
      env:
        - name: test111
          value: ttttttt
      envFrom:
          - configMapRef:
              name: airflow-conf
      volumeMounts:
        - name: airflow-home
          mountPath: /root/airflow
  restartPolicy: Never
  volumes:
    - name: airflow-home
      hostPath:
        path: /xxxxx/airflow
        type: DirectoryOrCreate


```