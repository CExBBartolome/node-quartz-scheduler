

Quartz is a distributed scheduling engine. This API allows to use Quartz with
node.js

Quartz does not expose HTTP services by himself. You'll need to build (maven) a
```war``` file from [quartz-http](https://github.com/nherment/quartz-http).

```
    var Quartz = require('quartz-scheduler')

    var scheduler = new Quartz({
      queue: {
        redis: {
          port: 6379,
          host: 'localhost',
          auth: null,
          options: {
            // look for more redis options in [node_redis](https://github.com/mranney/node_redis)
          }
        }
      },
      listen: 8001, // the exposed HTTP api port
      callbackURL: 'http://localhost:8001/api/job', // the FQDN for the exposed service
      quartzURL: 'http://localhost:8080/scheduler/api', //the Quartz HTTP endpoint
      concurrency: 5, // max number of jobs to run in parallel (jobs are queued),
      monitor: false  // whether to expose a job processing monitoring interface
    })

    scheduler.on('jobName', function(jobId, data, done) {
      // jobId: the same jobId that was given upon scheduling
      // data ==> {foo: bar}
      // process the job here
      done(err) // optional error if processing the job went wrong
    })

    scheduler.schedule('jobName', new Date(), {foo: bar}, function(err, jobId) {

      // jobId is a string

      scheduler.cancel(jobId, function(err) {
        // err if something went wrong
      })
    })
```
