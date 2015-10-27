# Socorro DevOps Ch34tsh33t

## Production -> Stage Submitter
* Running on i-aaee0d5c / ec2-52-27-158-113.us-west-2.compute.amazonaws.com
* Restart using **sudo systemctl restart socorro-submitter**

## Logging into AWS
* https://mozilla-webeng.signin.aws.amazon.com/console

## Metrics / Monitoring
* **I want to see our metrics in a cycling dashboard** : http://dashplay.io/0p98yofx
* **I want to see if the site went down** : http://stats.pingdom.com/98xgemgjzugt
* **I want to see if production is processing / collecting : https://app.datadoghq.com/dash/65215/socorro-prod (Ask JP for a login)
* **I need to see build / deploy logs and statuses** : https://leeroy.mocotoolsstaging.net/ (Ask JP for a login)

## How to "Rollback"
* Go to the deploy you want rolled back in Leeroy
* Go to the Console Output for that deploy in Leeroy
* Do a text search in your browser for **BEGINNING STATE**
* Take a copy/paste of the AMI we started with
* Go to the **Replace $ENV AMI** job in Leeroy
* Click on **Build With Parameters**
* Paste the AMI version into the **Parameters** box

## How to kick off a rerun of a deploy to stage
* Go to the deploy that failed/aborted and needs to be rerun
* Go to the Console Output for that deploy
* At the very top, click on **Full Log** .  This will open this very large logfile.
* Scroll up to the top, and find **Git Payload for copying into jenkins build parameter**
* Copy the entire json blob underneath the **Git Payload for cop....** line
* In Leeroy, click on **Back to Project**
* Click on **Build with Parameters**
* Paste in that entire json blob and then click build

## How to run a local consul session without hosing stage/prod (thanks Lars / Rhelmer! )
$ sudo systemctl stop consul socorro-processor
* run local single-node consul server
$ consul agent -server -bootstrap-expect 1 -data-dir /tmp/consul &
* ensure that no data is in local consul server
$ consulate kv backup
[]
* should only be a single member of this "cluster"
$ consul members
* load new config to test
$ cat socorro.consul.config.json | consulate kv restore
* new processor will use the local consul server
$ sudo systemctl start socorro-processor

## Script to kill nodes (uses old AWS api command line tools)
```as-set-instance-health $1 --status Unhealthy```
```sleep 1```
```ec2-terminate-instances $1```

