## Mechanical machine (possibly around 100'000 in count)

- Components
  - generate log streams in various rates - we want to collect them with 0% loss rate (this is assured by the event hub)

- Event hub
  - collects log streams with some persistency (1 day)

- Zipper
  - periodically collects logs from the event hub (every 30 mins during day, every 6 hours during night)
  - wraps the data in zip files

## The Cloud

- IoT hub
  - offers message routing to various endpoinds (one of which is blob storage)

- Blob storage
  - stores zip files with log dumps

- Kubernetes with anomaly detection workers
  - triggered by a message from IoT hub
    - the message points to the blob in the blob storage
  - Pods (many) with machine learning models in python (pods can run python apps)
    - they send the identified anomaly reports to the event hub

- Event hub
  - contains the anomaly reports for the technicians
  - collected by consumers

- Redis cache
  - provides caching of mapping of technicians to devices, for the "consumers" module

- 3rd party REST API 
  - provides mapping of devices to technicians
  - unreliable - needs caching

- Consumers
  - partitioned application that consumes the reports - we need to repeatedly ask the REST API
  - simple app written by the cloud platform team

- Notification hub
  - sends reports with identified devices to the technicians
