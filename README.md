<!-- This file was automatically generated by the `build-harness`. Make all changes to `README.yaml` and run `make readme` to rebuild this file. -->

# ecs-logzio-logs-aggregator [![Build Status](https://travis-ci.org/aleks-fofanov/ecs-logzio-logs-aggregator.svg?branch=master)](https://travis-ci.org/aleks-fofanov/ecs-logzio-logs-aggregator) [![Latest Release](https://img.shields.io/github/release/aleks-fofanov/ecs-logzio-logs-aggregator.svg)](https://github.com/aleks-fofanov/ecs-logzio-logs-aggregator/releases/latest)


Customized Fluentd image designed to be deployed to AWS ECS cluster and ship containers logs to
[Logz.io](https://logz.io/).


---


It's 100% Open Source and licensed under the [APACHE2](LICENSE).









## Introduction

This docker image is a customized version Fluentd image (`v1.4.2-debian-2.0`) designed to receive logs from your
ECS containers and ship them to [Logz.io](https://logz.io/).

**Implementation notes and Warnings**:
- Pass your Logz.io listener URL and token via environment variables (see below)
- Pefix your containers log tags with `docker.`, otherwise logs won't be processed
  ```json
  "logConfiguration": {
      "logDriver": "fluentd",
      "options": {
          "fluentd-address": "fluentd-address:24224",
          "tag": "docker.backend-app"
      }
  }
  ```

Fluentd is configured to be `json` logs friendly, meaning that it would try to parse log records as json
  and [set fields to the resulting record from parsed json](https://docs.fluentd.org/filter/parser#reserve_data).

**Exposed ports**:
- TCP 0.0.0.0 9880 (HTTP for healthcheck purposes only)
- TCP & UPD 0.0.0.0 24224 (Logs collection & forwarding to Logz.io)

You can use the following command in ECS to setup a healthcheck for the container:
   `["CMD-SHELL", "curl http://0.0.0.0:9880/fluentd.healthcheck?json=%7B%22log%22%3A+%22health+check%22%7D || exit 1"]`

**Recommended deployment options**:
- Daemonset on ECS container instances (EC2)
- Autoscaling ECS Service on Fargate (you would also need to configure ECS Service Discovery in this case)

**Configuration**. Use the following environment variables to configure Logz.io plugin:
| Environment Variable | Description |
|---|---|
| **LOGZIO_URL** | **Required**. Your Logz.io listener URL. Replace `<LISTENER-URL>` with your region's listener URL. For more information on finding your account's region, see [Account region](https://docs.logz.io/user-guide/accounts/account-region.html) in the Logz.io Docs. |
| **LOGZIO_TOKEN** | **Required**. Your Logz.io account token. Replace `<ACCOUNT-TOKEN>` with the [token](https://app.logz.io/#/dashboard/settings/general) of the account you want to ship to. |

Please refer to
[Logz.io Fluend plugin parameters](https://github.com/logzio/fluent-plugin-logzio#parameters)
documentation for more context.


## Quick Start

To run container locally for testing:
```bash
docker run -d --name fluentd \
-e LOGZIO_URL=YOUR_LOGZIO_LISTERNER_URL \
-e LOGZIO_TOKEN=YOUR_LOGZIO_TOKEN \
-p 24224:24224 \
aleksfofanov/ecs-logzio-logs-aggregator
```
Then you can configure your containers to ship logs to `localhost:24224`.




## Makefile Targets
```
Available targets:

  help                                Help screen
  help/all                            Display help for all targets
  help/short                          This help short screen

```




## References

For additional context, refer to some of these links. 

- [Scalable log solution aggregator with AWS Fargate, Fluentd, and Amazon Kinesis Data Firehose](https://aws.amazon.com/blogs/compute/building-a-scalable-log-solution-aggregator-with-aws-fargate-fluentd-and-amazon-kinesis-data-firehose/) - Great example of how to build a scalable log solution on AWS
- [Centralized Container Logging with Fluent Bit](https://aws.amazon.com/blogs/opensource/centralized-container-logging-fluent-bit/) - Another example of centralized logs collection & aggregation


## Help

**Got a question?**

File a GitHub [issue](https://github.com/aleks-fofanov/ecs-logzio-logs-aggregator/issues).


