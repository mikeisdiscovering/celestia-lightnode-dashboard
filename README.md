# celestia-lightnode-dashboard
A celestia light node grafana dashboard

## Prerequisites

1. Export your light node metrics, you can refer [lightnodeexportmetrics.md](https://github.com/mikeisdiscovering/celestia-lightnode-dashboard/blob/main/lightnodeexportmetrics.md).
2. Install and configure prometheus and grafana in your monitor server.you can refer [configurepromethues_grafana.md](https://github.com/mikeisdiscovering/celestia-lightnode-dashboard/blob/main/configurepromethues_grafana.md).

## How to use this dashboard

1. import dashboard using [Celestia-lightnode-dashboard.json](https://raw.githubusercontent.com/mikeisdiscovering/celestia-lightnode-dashboard/main/Celestia-lightnode-dashboard.json).

   select previous prometheus as data source,  input your node id. Then click import。

2. You can check your dashboard. It display CPU ,RAM ,Disk usage, and many celestia light node metrics. 

3. If you have upgrade your node , you can click the job_version on the left corner , change it to current node version.

4. Using the dashboard, you can check your light node status easily, and you can set alert to improve your light node performance. Such as disk alert, CPU usage alert.

## Final result
![VZmaGH.jpeg](https://i.328888.xyz/2023/05/15/VZmaGH.jpeg)

  

