# Services

You can control individual services with the `so-<component>-<verb>` scripts. You can see a list of all of these scripts with the following command:


```
ls /usr/sbin/so-*
```

The following examples are for [Zeek](zeek.md), but you could substitute whatever service you're trying to control ([Logstash](logstash.md), [Elasticsearch](elasticsearch.md), etc.).

Start [Zeek](zeek.md):


```
sudo so-zeek-start
```

Stop [Zeek](zeek.md):


```
sudo so-zeek-stop
```

Restart [Zeek](zeek.md):


```
sudo so-zeek-restart
```