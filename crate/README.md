# Supported tags and respective `Dockerfile` links

-	[`latest`, `0.50`, `0.50.5` (*Dockerfile*)](https://github.com/crate/docker-crate/blob/afe4de88bbe1a55443f7daa91fc78b50b41eb8b8/Dockerfile)
-	[`0.49`, `0.49.6` (*Dockerfile*)](https://github.com/crate/docker-crate/blob/8762d54054d9ed9d76a3bba0f9e6fa38acd34c2a/Dockerfile)

For more information about this image and its history, please see [the relevant manifest file (`library/crate`)](https://github.com/docker-library/official-images/blob/master/library/crate). This image is updated via pull requests to [the `docker-library/official-images` GitHub repo](https://github.com/docker-library/official-images).

For detailed information about the virtual/transfer sizes and individual layers of each of the above supported tags, please see [the `crate/tag-details.md` file](https://github.com/docker-library/docs/blob/master/crate/tag-details.md) in [the `docker-library/docs` GitHub repo](https://github.com/docker-library/docs).

# What is Crate?

Crate is an Elastic SQL Data Store. Distributed by design, Crate makes centralized database servers obsolete. Realtime non-blocking SQL engine with full blown search. Highly available, massively scalable yet simple to use.

[Crate](https:/crate.io/)

![logo](https://raw.githubusercontent.com/docker-library/docs/master/crate/logo.png)

## How to use this image

```console
$ docker run -d -p 4200:4200 -p 4300:4300 crate:latest
```

### Attach persistent data directory

```console
$ docker run -d -p 4200:4200 -p 4300:4300 -v <data-dir>:/data crate
```

### Use custom Crate configuration

```console
$ docker run -d -p 4200:4200 -p 4300:4300 crate -Des.config=/path/to/crate.yml
```

Any configuration settings may be specified upon startup using the `-D` option prefix. For example, configuring the cluster name by using system properties will work this way:

```console
$ docker run -d -p 4200:4200 -p 4300:4300 crate crate -Des.cluster.name=cluster
```

For further configuration options please refer to the [Configuration](https://crate.io/docs/stable/configuration.html) section of the online documentation.

### Environment

To set environment variables for Crate Data you need to use the `--env` option when starting the docker image.

For example, setting the heap size:

```console
$ docker run -d -p 4200:4200 -p 4300:4300 --env CRATE_HEAP_SIZE=32g crate
```

## Multicast

Crate uses multicast for node discovery by default. However, Docker does only support multicast on the same host. This means that nodes that are started on the same host will discover each other automatically, but nodes that are started on different hosts need unicast enabled.

You can enable unicast in your custom `crate.yml`. See also: [Crate Multi Node Setup](https://crate.io/docs/en/latest/best_practice/multi_node_setup.html).

Due to its architecture, Crate publishes the host it runs on for discovery within the cluster. Since the address of the host inside the docker container differs from the actual host the docker image is running on, you need to tell Crate to publish the address of the docker host for discovery.

```console
$ docker run -d -p 4200:4200 -p 4300:4300 crate crate -Des.network.publish_host=host1.example.com:
```

If you change the transport port from the default `4300` to something else, you also need to pass the publish port to Crate.

```console
$ docker run -d -p 4200:4200 -p 4321:4300 crate crate -Des.transport.publish_port=4321
```

### Example Usage in a Multinode Setup

```console
$ HOSTS='crate1.example.com:4300,crate2.example.com:4300,crate3.example.com:4300'
$ HOST=crate1.example.com
$ docker run -d \
	-p 4200:4200 \
	-p 4300:4300 \
	--name node1 \
	--volume /mnt/data:/data \
	--env CRATE_HEAP_SIZE=8g \
	crate:latest \
	crate -Des.cluster.name=cratecluster \
		  -Des.node.name=crate1 \
		  -Des.transport.publish_port=4300 \
		  -Des.network.publish_host=$HOST \
		  -Des.multicast.enabled=false \
		  -Des.discovery.zen.ping.unicast.hosts=$HOSTS \
		  -Des.discovery.zen.minimum_master_nodes=2
```

# License

View [license information](https://github.com/crate/crate/blob/master/LICENSE.txt) for the software contained in this image.

# Supported Docker versions

This image is officially supported on Docker version 1.8.1.

Support for older versions (down to 1.0) is provided on a best-effort basis.

# User Feedback

## Documentation

Documentation for this image is stored in the [`crate/` directory](https://github.com/docker-library/docs/tree/master/crate) of the [`docker-library/docs` GitHub repo](https://github.com/docker-library/docs). Be sure to familiarize yourself with the [repository's `REAMDE.md` file](https://github.com/docker-library/docs/blob/master/README.md) before attempting a pull request.

## Issues

If you have any problems with or questions about this image, please contact us through a [GitHub issue](https://github.com/crate/docker-crate/issues).

If you have any questions or suggestions we would be very happy to help you. So, feel free to swing by our IRC channel `#crate` on [Freenode](http://freenode.net).

For further information and official contact please visit [https://crate.io](https://crate.io).

## Contributing

You are very welcome to contribute features or fixes! Before we can accept any pull requests to Crate Data we need you to agree to our [CLA](https://crate.io/community/contribute/). For further information please refer to [CONTRIBUTING.rst](https://github.com/crate/crate/blob/master/CONTRIBUTING.rst).
