TP4-Docker-Spring
===================

On installe le plugin docker-maven-plugin en ajoutant au fichier pom.xml de chaque service
```
<plugin>
				<groupId>com.spotify</groupId>
				<artifactId>docker-maven-plugin</artifactId>
				<configuration>
					<imageName>${project.artifactId}</imageName>
					<baseImage>java:8</baseImage>
					<entryPoint>["java", "-jar", "/${project.build.finalName}.jar"]         </entryPoint>
					
					<resources>
						<resource>
							<targetPath>/</targetPath>
							<directory>${project.build.directory}</directory>
							<include>${project.build.finalName}.jar</include>
						</resource>
					</resources>

				</configuration>
				<executions>
					<execution>
						<phase>package</phase>
						<goals>
							<goal>build</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
```
On fait le build mvn package
On exécute la commande
```
docker images
```
On remarque l'apparition des images docker avec leur ID
On lance le service Config :
```
docker run -it -p 8888:8888 config-service --spring.cloud.config.server.git.uri=https://github.com/souuu/config-spring
```

Pour avoir l'IP de spring.cloud.config.uri, on exécute 
```
docker inspect -f "{{ .NetworkSettings.IPAddress }}" d78f31081a73
```
tel que d78f31081a73 est l'ID du service config lancé, pour avoir cet ID, il suffit de lancer
```
docker ps -a
```
après on lance les trois autres services dans cet ordre

Discovery
```
docker run -it -p 8761:8761 discovery-service -e --spring.cloud.config.uri=http://172.17.0.2:8888
```
Proxy
```
docker run -it -p 9999:9999 proxy-service
```
Product
```
docker run -it -p 8080:8080 product-service -e --spring.cloud.config.uri=http://172.17.0.2:8888
```