# Job Roundup

Benchmarking various Ruby background job engines.

## To Prepare

```
brew install postgresql@16 redis
bundle
rake db:create
bin/rails g good_job:install
rake solid_queue:install:migrations
rake db:migrate
export RUBY_YJIT_ENABLE=1
```

## Between Runs

```
redis-cli flushall
rake db:drop db:create db:migrate
```

## Run the Benchmarks

```
ruby bench.rb
```

## Results

You'll see something similar to:

```
> ruby ./bench.rb
2024-08-02T22:52:48.585Z pid=555618 tid=bqlq INFO: Sidekiq 7.3.0 connecting to Redis with options {:size=>10, :pool_name=>"internal", :url=>nil}
ruby 3.3.4 (2024-07-09 revision be1089c8ec) +YJIT [x86_64-linux]
{:rails=>"7.1.3.4", :good_job=>"3.99.1", :sidekiq=>"7.3.0", :solid_queue=>"0.3.4"}
Benchmarking with 1000 jobs per iteration
Warming up --------------------------------------
               good_job-push      0.575 i/s -       1.000 times in 1.737648s (1.74s/i)
           good_job-pushbulk      5.825 i/s -       6.000 times in 1.030015s (171.67ms/i)
            solid_queue-push      0.522 i/s -       1.000 times in 1.913994s (1.91s/i)
                sidekiq-push     15.545 i/s -      16.000 times in 1.029294s (64.33ms/i)
          sidekiq-native-enq     39.506 i/s -      40.000 times in 1.012504s (25.31ms/i)
     sidekiq-native-enq-bulk    291.926 i/s -     308.000 times in 1.055061s (3.43ms/i)
async-job-adapter-active_job     13.128 i/s -      14.000 times in 1.066402s (76.17ms/i)
Calculating -------------------------------------
               good_job-push      0.543 i/s -       1.000 times in 1.842438s (1.84s/i)
           good_job-pushbulk      5.705 i/s -      17.000 times in 2.979910s (175.29ms/i)
            solid_queue-push      0.475 i/s -       1.000 times in 2.106016s (2.11s/i)
                sidekiq-push     14.629 i/s -      46.000 times in 3.144498s (68.36ms/i)
          sidekiq-native-enq     42.134 i/s -     118.000 times in 2.800589s (23.73ms/i)
     sidekiq-native-enq-bulk    292.835 i/s -     875.000 times in 2.988028s (3.41ms/i)
async-job-adapter-active_job     15.231 i/s -      39.000 times in 2.560513s (65.65ms/i)

Comparison:
     sidekiq-native-enq-bulk:       292.8 i/s 
          sidekiq-native-enq:        42.1 i/s - 6.95x  slower
async-job-adapter-active_job:        15.2 i/s - 19.23x  slower
                sidekiq-push:        14.6 i/s - 20.02x  slower
           good_job-pushbulk:         5.7 i/s - 51.33x  slower
               good_job-push:         0.5 i/s - 539.53x  slower
            solid_queue-push:         0.5 i/s - 616.72x  slower
```

TODO: Execution profiling?

## Profiling

Something like:

```
bundle exec ruby-prof -p graph_html -m 1 -f profile.html bin/rails -- r bench.rb
```

TODO: Vernier integration?