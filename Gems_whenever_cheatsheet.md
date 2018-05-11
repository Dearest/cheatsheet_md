## whenever

#### init  `初始化`

```bash
$wheneverize .
```

This will create an initial `config/schedule.rb`

####show `查看`

```bash
$wheneven
```

This will simply show you your `schedule.rb` file converted to cron syntax. It does not read or write your crontab file

#### execute `执行`

```bash
$ whenever --update-crontab
You can list installed cron jobs using 
$ crontab -l
```

write your crontab file

#### example `示例` `例子` `eg`

```ruby
every 3.hours do # 1.minute 1.day 1.week 1.month 1.year is also supported
  runner "MyModel.some_process"
  rake "my:rake:task"
  command "/usr/bin/my_great_command"
end

every 1.day, at: '4:30 am' do
  runner "MyModel.task_to_run_at_four_thirty_in_the_morning"
end

every 1.day, at: ['4:30 am', '6:00 pm'] do
  runner "Mymodel.task_to_run_in_two_times_every_day"
end

every :hour do # Many shortcuts available: :hour, :day, :month, :year, :reboot
  runner "SomeModel.ladeeda"
end

every :sunday, at: '12pm' do # Use any day of the week or :weekend, :weekday
  runner "Task.do_something_great"
end

every '0 0 27-31 * *' do
  command "echo 'you can use raw cron syntax too'"
end

# run this task only on servers with the :app role in Capistrano
# see Capistrano roles section below
every :day, at: '12:20am', roles: [:app] do
  rake "app_server:task"
end
```

