```xml
<repositories>
	<repository>
		<id>public</id>
		<name>aliyun nexus</name>
		<url>http://maven.aliyun.com/nexus/content/groups/public/</url>
		<releases>
			<enabled>true</enabled>
		</releases>
	</repository>
</repositories>

<pluginRepositories>
	<pluginRepository>
		<id>public</id>
		<name>aliyun nexus</name>
		<url>http://maven.aliyun.com/nexus/content/groups/public/</url>
		<releases>
			<enabled>true</enabled>
		</releases>
		<snapshots>
			<enabled>false</enabled>
		</snapshots>
	</pluginRepository>
</pluginRepositories>
```
