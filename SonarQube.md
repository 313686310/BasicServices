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


