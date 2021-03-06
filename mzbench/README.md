# Scenarios

Folder [scenarios](scenarios/) contains .bdl files ([Benchmark Definition Language](https://github.com/mzbench/mzbench/blob/master/doc/scenarios/spec.md)) that represents different testing scenarios for MZbench.


List of scenarios that we will test.
- [Fan-in](scenarios/fan_in.bdl)
- [Fan-out](scenarios/fan_out.bdl)
- [1-to-1](scenarios/1to1.bdl)
- [Burnout](scenarios/burnout.bdl)

A prerequisite for running Fan-in and Fan-out test scenarios is to have Mainflux instance where you should:
- Create one Channel
- Create two Things
- Connect both Things to Channel

Required parameters are then set with running mzbench with --env param:

`$ ./bin/mzbench run --env foo=bar --env n=42`

or when using MZBench dashboard by importing and setting environment variables from scenario.


| Environment variable     | Description                                                      | Default   |
|--------------------------|------------------------------------------------------------------|-----------|
| MF_MZBENCH_MQTT_ENDPOINT | IP/domain of MQTT endpoint on Mainflux                           | 127.0.0.1 |
| MF_MZBENCH_MQTT_PORT     | Port of MQTT endpoint on Mainflux                                | 1883      |
| MF_MZBENCH_CHANNEL_ID    | ID of pre-provisioned channel on which both things are connected |           |
| MF_MZBENCH_THING_1_ID    | First thing ID of pre-provisioned Mainflux thing                 |           |
| MF_MZBENCH_THING_1_KEY   | First thing key of pre-provisioned Mainflux thing                |           |
| MF_MZBENCH_THING_2_ID    | Second thing ID of pre-provisioned Mainflux thing                |           |
| MF_MZBENCH_THING_2_KEY   | Second thing key of pre-provisioned Mainflux thing               |           |

Scenarios have its own parameters (like duration of test, rate of publishing...) that are set through environment variables, also. You can redefine an environment variable even during the benchmark without interrupting the benchmark itself with [change env](https://github.com/mzbench/mzbench/blob/master/doc/cli.md#change_env) command.

## Fan-in

In [fan-in](https://en.wikipedia.org/wiki/Fan-in) scenario numerous MQTT clients are sending messages on same Mainflux channel, but every client to exclusive subtopic on that channel. One subscriber is consuming all those messages.

| Environment variable | Description                                                                                      | Default |
|----------------------|--------------------------------------------------------------------------------------------------|---------|
| MF_MZBENCH_PUB_NUM   | Number of publishers                                                                             | 10000   |
| MF_MZBENCH_PUB_RATE  | Message rate per publihser in rps. Number of messages that every publisher is sending per second | 1       |
| MF_MZBENCH_PUB_TIME  | Duration of publishing messages in minutes                                                       | 5       |
| MF_MZBENCH_MSG_SIZE  | Size of messages in bytes. Messages are a sequence of random bytes                               | 100     |
| MF_MZBENCH_QOS       | MQTT QoS level                                                                                   | 2       |

## Fan-out

In [fan-out](https://en.wikipedia.org/wiki/Fan-out) scenario one MQTT client is sending messages on Mainflux channel. Numerous subscribers are connected to that channel and consuming  messages.

| Environment variable | Description                                                                  | Default |
|----------------------|------------------------------------------------------------------------------|---------|
| MF_MZBENCH_SUB_NUM   | Number of subscribers                                                        | 10000   |
| MF_MZBENCH_PUB_RATE  | Message rate in rps. Number of messages that publisher is sending per second | 1       |
| MF_MZBENCH_PUB_TIME  | Duration of publishing messages in minutes                                   | 5       |
| MF_MZBENCH_MSG_SIZE  | Size of messages in bytes. Messages are a sequence of random bytes           | 100     |
| MF_MZBENCH_QOS       | MQTT QoS level                                                               | 2       |

## 1-to-1

In 1-to-1 test numerous clients subscribe to an exclusive Mainflux channel and the same clients send messages on that channel to themselves.

A prerequisite for running 1-to-1 test scenarios is to have Mainflux instance where you should:
- Create 1K Things (or some other size that will go in MF_MZBENCH_PUB_NUM environment variable)
- Create same number of Channels
- Create JSON array of Things' IDs in file `things_id.json`
- Create JSON array of corresponding Things' keys in file `things_key.json`
- Create JSON array of corresponding Cahnnels' IDs in file `channels_id.json`
- First Thing from list should be connected to first Channel, second Thing should be connected to second Channel and so on. Checkout [Mainflux provisioning tool](https://github.com/mainflux/mainflux/tree/master/tools/provision) to help you achieve this.

| Environment variable     | Description                                                                                      | Default   |
|--------------------------|--------------------------------------------------------------------------------------------------|-----------|
| MF_MZBENCH_MQTT_ENDPOINT | IP/domain of MQTT endpoint on Mainflux                                                           | 127.0.0.1 |
| MF_MZBENCH_MQTT_PORT     | Port of MQTT endpoint on Mainflux                                                                | 1883      |
| MF_MZBENCH_PUB_NUM       | Number of publishers                                                                             | 1000      |
| MF_MZBENCH_PUB_RATE      | Message rate per publihser in rps. Number of messages that every publisher is sending per second | 1         |
| MF_MZBENCH_PUB_TIME      | Duration of publishing messages in minutes                                                       | 5         |
| MF_MZBENCH_MSG_SIZE      | Size of messages in bytes. Messages are a sequence of random bytes                               | 100       |
| MF_MZBENCH_QOS           | MQTT QoS level                                                                                   | 2         |

## Burnout 
Burnout test is similar to the `Fan-In` just that message rate function is `comb` not a constant. Test is intended to be run for a long period of time to detect possible resource problem in deployment. By default it runs for 300 min, publishing for 1 min and then pause for 9 min.