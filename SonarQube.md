# Windows 系统下使用Docker搭建SonarQube

编写docker compose  (https://docs.sonarqube.org/10.0/instance-administration/security/#settings-encryption)

```YML
version: "3"

services:
  sonarqube:
    image: sonarqube:community
    depends_on:
      - db
    environment:
      SONAR_JDBC_URL: jdbc:postgresql://db:5432/sonar
      SONAR_JDBC_USERNAME: sonar
      SONAR_JDBC_PASSWORD: sonar
    volumes:
      - sonarqube_data:/opt/sonarqube/data
      - sonarqube_extensions:/opt/sonarqube/extensions
      - sonarqube_logs:/opt/sonarqube/logs
    ports:
      - "9000:9000"
  db:
    image: postgres:12
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonar
    volumes:
      - postgresql:/var/lib/postgresql
      - postgresql_data:/var/lib/postgresql/data

volumes:
  sonarqube_data:
  sonarqube_extensions:
  sonarqube_logs:
  postgresql:
  postgresql_data:
  ```
  
  windows 系统默认virtual memory不足会报如下错误：  

  <span style="background-color: #fc9c93;">max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]</span>
  
  需要执行命令修改系统默认配置
  ```powershell
  wsl -d docker-desktop
  sysctl -w vm.max_map_count=262144
  exit
  ```
  再次启动,运行成功！！！

## 检测本地.NET程序
1.sonar生成token

  ![image](https://user-images.githubusercontent.com/13936681/236361558-c79c4435-f686-44d6-8286-96995bd6be4e.png)
  
2.安装扫描工具
  ```
  $ dotnet tool install --global dotnet-sonarscanner
  ```
3.执行扫描脚本
  ```
  $ dotnet sonarscanner begin /k:"project-key" /d:sonar.host.url=http://xxx.xxx.xxx.xxx:9000  /d:sonar.login="<token>"
  $ dotnet build <path to solution.sln>
  $ dotnet sonarscanner end /d:sonar.login="<token>"
  ```
  ## 查看效果

