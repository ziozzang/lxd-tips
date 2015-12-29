How to Migrate container to another host
====

This is not for live migration. just pack & unpack migration.

1.
Stop the container
```
lxc stop foo
```

2.
The files are in under /var/lib/lxd/containers/ directory. just pack this directory into tgz.
```
cd /var/lib/lxd/containers/
tar -czvf foo.tgz foo
```

3.
Copy file into another host or some..

4.
unpakc foo.tgz at same location. but, LXD use sqlite to save metadata. so, you must create dummy container. and stop it.
```
# create dummy container to overwrite
lxc launch foo-template foo
# stop container
lxc stop foo

cd /var/lib/lxd/containers/
# remove container folder
rm -rf foo

# over write...
tar -xzvf foo.tgz

```

5.
done. just restart container.

```
lxc start foo
```
