---
layout: post
title: AWS CloudWatch 대시보드 사용하기
date: 2021-11-12 23:20:00 +0900
description: babble 팀 프로젝트
img: Babble-Text.jpeg
tags: aws study
---

> CloudWatch
> 로그, 지표 및 이벤트 형태로 모니터링 및 운영 데이터를 수집하여 AWS와 온프레미스 서버에서 실행되는 AWS 리소스, 애플리케이션 및 서비스에 대한 통합된 보기를 제공

 

## **사용한 이유**

Web Server와 Was는 로그 파일이 생성됩니다. 이 로그 파일을 확인하기 위해서는 직접 EC2 인스턴스에 접속해서 로그 파일을 열어보는 수 밖에 없었습니다. 때문에 개발의 편의와 손쉬운 정보 수집, 빠른 문제 상황에 대응하기 위한 로그 시각화 수단이 필요했습니다.

일반적으로 로그 시각화 용도로 ELK(Elastic Logstash Kibana) 스택 중 하나인 Kibana, 매트릭스 지표 시각화에 특화된 Grafana를 많이 사용하지만, 저희 팀 프로젝트는 AWS를 사용하는 환경이고, CloudWatch에서도 필요한 기능이 모두 제공되었기 때문에 CloudWatch를 통해 로그 시각화를 진행했습니다.

 

## **적용하기**

1. web-server 에서 cloud-watch 디렉토리를 생성합니다.

```
$ mkdir cloud-watch
```

2. Python 이 없을 경우 설치를 합니다.

```
$ sudo apt-get install python
```

3. cloud-watch 디렉토리로 이동 후 설치합니다.

```
$ curl <https://s3.amazonaws.com/aws-cloudwatch/downloads/latest/awslogs-agent-setup.py> -O $ sudo python ./awslogs-agent-setup.py --region ap-northeast-2
```

4. 아래 명령어를 실행해서 설치해 줍니다.

```
$ sudo python ./awslogs-agent-setup.py --region ap-northeast-2
```

 

기본 설정은 전부 Default 로 진행했습니다. More log files to configure? 는 N 하셔야 루프 탈출이 가능합니다.

- 연결할 EC2 인스턴스의 IAM 역할을 CloudWatch 로 설정합니다.
- IAM 역할이 지정되지 않으면 Default 가 아닌 추가 설정을 했어야 합니다.

설정중에 Choose Log Event timestamp format(3번), Choose initial position of upload(2번) 으로 테스트 서버는 설정함

 

5. 로그 그룹이름을 설정해 줍니다. (팀 이름으로 설정, babble- 뒤에 각 sys, access, error 로 지정)

```
$ sudo vi /var/awslogs/etc/awslogs.conf

[/var/log/syslog]
datetime_format = %b %d %H:%M:%S
file = /var/log/syslog
buffer_duration = 5000
log_stream_name = {instance_id}     # i-0af359bf101a6c5d4
initial_position = start_of_file
log_group_name = babble-sys  # AWS 에서 표시될 그룹 이름

[/var/log/nginx/access.log]
datetime_format = %d/%b/%Y:%H:%M:%S %z
file = /var/log/nginx/access.log
buffer_duration = 5000
log_stream_name = access.log
initial_position = end_of_file
log_group_name = babble-access

[/var/log/nginx/error.log]
datetime_format = %Y/%m/%d %H:%M:%S
file = /var/log/nginx/error.log
buffer_duration = 5000
log_stream_name = error.log
initial_position = end_of_file
log_group_name = babble-error
```

 

6. 설정이 끝나면 재시작 합니다.

```
$ sudo service awslogs restart
```

7. EC2-Matric 을 수집합니다.

다음 명령어를 순차적으로 입력해줍니다.

```
$ wget <https://s3.amazonaws.com/amazoncloudwatch-agent/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb>
$ sudo dpkg -i -E ./amazon-cloudwatch-agent.deb
```

8. 해당 경로의 파일을 수정합니다.

```
$ sudo vi /opt/aws/amazon-cloudwatch-agent/bin/config.json
```

9. 입력 후 다음 단계를 진행합니다.

```
{
        "agent": {
                "metrics_collection_interval": 60,
                "run_as_user": "root"
        },
        "metrics": {
                "metrics_collected": {
                        "disk": {
                                "measurement": [
                                        "used_percent",
                                        "used",
                                        "total"
                                ],
                                "metrics_collection_interval": 60,
                                "resources": [
                                        "*"
                                ]
                        },
                        "mem": {
                                "measurement": [
                                        "mem_used_percent",
                                        "mem_total",
                                        "mem_used"
                                ],
                                "metrics_collection_interval": 60
                        }
                }
        }
}
```

10. AWS 에서 Cloud Watch 의 대시보드로 이동합니다.

 

11. 대시보를 생성합니다.

 

12. 유형은 행을 선택합니다.

 

13. 원본 데이터로 지표를 선택합니다.

 

14. CPU Utilization, Network In / Out, mem_used_percent, disk_used_percent 등 원하는 지표를 선택합니다.

 

15. 더 추가할 내용을 추가합니다.

 

9번 작업 을 통해 추가해준 디스크, 메모리 사용량 관련 위젯은 시간이 어느정도(10분 정도) 지나야 노출되는 것으로 보입니다.

 

16. 추가가 완료된 모습을 볼 수 있습니다.

- 앞서 web-server 에서 CloudWatch 설정을 통해 메모리 사용률, 디스크 사용률(ext4)을 확인할 수 있습니다.
- 앞서 서버에서 새롭게 설치하고 설정해준 시점을 기점으로 기록이 되므로 일정 시간이 지나고 나야 쌓인 로그를 확인할 수 있습니다.

이름은 제목에 마우스를 올리면 편집 가능합니다.

항상 변경하면 중간중간 대시보드 저장을 잊지말고 눌러줍시다.

 

17. Nginx 에서 생성된 log 파일을 CloudWatch 에서 볼 수 있도록 설정을 추가합니다.

 

18. 로그 그룹을 선택합니다.

 

19. 쿼리 실행을 누르면 잠시 후 하단에 로그가 출력됩니다. 마지막으로 우측상단의 위젯 생성을 누르면 정상적으로 추가됩니다.

 

20. test 서버 혹은 was 의 spring log 파일도 등록해줍니다.

```
$ sudo vi /var/awslogs/etc/awslogs.conf
// 테스트 서버에 적용한 파일 내용
// 테스트 서버 기준 5번에서 작성한 내용에 이어서 아래쪽에 추가해 줬습니다.
// 배포 서버는 was 서버를 따로 운영하기 때문에 따로 설치하고 아래 내용을 작성하는 작업을 해야합니다.

[/home/ubuntu/was/was-logs/error.log]
datetime_format = %d/%b/%Y:%H:%M:%S %z
file = /home/ubuntu/was/was-logs/error.log
buffer_duration = 5000
log_stream_name = babble/test/was/error.log
initial_position = end_of_file
log_group_name = babble-was-test-error

[/home/ubuntu/was/was-logs/warn.log]
datetime_format = %d/%b/%Y:%H:%M:%S %z
file = /home/ubuntu/was/was-logs/warn.log
buffer_duration = 5000
log_stream_name = babble/test/was/warn.log
initial_position = end_of_file
log_group_name = babble-was-test-warn

[/home/ubuntu/was/was-logs/info.log]
datetime_format = %d/%b/%Y:%H:%M:%S %z
file = /home/ubuntu/was/was-logs/info.log
buffer_duration = 5000
log_stream_name = babble/test/was/info.log
initial_position = end_of_file
log_group_name = babble-was-test-info

[/home/ubuntu/was/was-logs/debug.log]
datetime_format = %d/%b/%Y:%H:%M:%S %z
file = /home/ubuntu/was/was-logs/debug.log
buffer_duration = 5000
log_stream_name = babble/test/was/debug.log
initial_position = end_of_file
log_group_name = babble-was-test-debug

[/home/ubuntu/was/was-logs/trace.log]
datetime_format = %d/%b/%Y:%H:%M:%S %z
file = /home/ubuntu/was/was-logs/trace.log
buffer_duration = 5000
log_stream_name = babble/test/was/trace.log
initial_position = end_of_file
log_group_name = babble-was-test-trace
```

21. 20번 작업이 끝나면 잊지말고 새로고침을 해줍니다.

```
$ sudo service awslogs restart
```


CloudWatch 를 활용하여 시각화된 그래프로 표현이 가능합니다. 로그의 경우 실제 에러 메세지를 확인해야하는 상황이 많았기 때문에 Text 로 출력되도록 구성하였습니다.



![img](https://blog.kakaocdn.net/dn/o0kdk/btrjlO8gonU/uRciqnHB5DL06cwUhuxg81/img.png)

![img](https://blog.kakaocdn.net/dn/NJTda/btrjlPsAjXW/6kHkkF2MWCW4am7nP0lscK/img.png)

![img](https://blog.kakaocdn.net/dn/c16mx5/btrjlQZjL2h/E1nBjGj7cTNX2gr0w4dhv0/img.png)



 

## **트러블 슈팅**

error.log 가 로그 그룹에 추가되지 않음

\- 에러 레벨 설계를 잘못하여 error 레벨의 로그가 발생하지 않았습니다.

\- error, debug, info 레벨의 수준을 변경했습니다.

\- 로그의 경우 새로운 로그가 정상적으로 생성되면 로그 그룹에 추가됩니다.

 

error 메세지의 모호함

\- "해당 방은 입장할 수 없습니다." 이와 같은 메세지는 어떤 방에서 에러가 발생했는지 추측하기가 어려웠습니다.

\- "[54]번방은 입장할 수 없습니다." 이처럼 에러가 발생한 곳을 특정하여 신속히 에러가 발생한 부분을 파악할 수 있었습니다.

 

Timestamp 이슈

\- UTC+0 의 시간이 나왔기 때문에 9시간의 시간적 오차가 발생했습니다.

\- 로그의 시간을 UTC+9 로 설정해주고 CloudWatch 의 쿼리에 포함된 @Timestamp 옵션을 제거했습니다.