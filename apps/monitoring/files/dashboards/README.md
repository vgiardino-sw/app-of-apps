# API Metrics Grafana Dashboard Documentation

This documentation details the rows and panels of the Grafana dashboard, including the description of each metric, the PromQL query used, and the purpose of each panel.

## Requests

### Panel 1: Currently Requests

- **Description:** Tracks the number of HTTP requests currently being processed by the API. It helps identify potential bottlenecks in request processing.
- **PromQL Query:** `sum(http_currently_requests)`
- **Use:** This metric is crucial for understanding the load on the API at any given time and ensuring that the service can handle currently traffic efficiently.

### Panel 2: Total Requests

- **Description:** Displays the total number of HTTP requests made to the API over time separated by http method, status and endpoint. It provides an overview of the API's traffic.
- **PromQL Query:** `sum(http_requests_total) by (method, endpoint, http_status)`
- **Use:** This panel helps in monitoring the overall demand on the API, making it easier to correlate spikes in traffic with application performance.

### Panel 2: Total Requests Per Hour

- **Description:** Displays the total number of HTTP requests made to the API separated by hours.
- **PromQL Query:** `increase(http_requests_total[1h])`
- **Use:** This panel is useful to see how many requests each endpoint receives in an hour.

  ![total_reqs](https://github.com/user-attachments/assets/6f5bae64-a31b-444f-b305-199630989035)

## Success/Failure Rate

### Panel 1: Success Requests Rate

- **Description:** Measures the rate of successful HTTP requests (status code 2xx) over time. It is an indicator of the API's reliability.
- **PromQL Query:** `sum(rate(http_requests_total{http_status=~"2.."}[1m])) / sum(rate(http_requests_total[1m]))`
- **Use:** This metric helps ensure that the majority of requests to the API are being successfully processed, which is vital for maintaining a good user experience.

### Panel 2: Failure Requests Rate

- **Description:** Tracks the rate of failed HTTP requests (status codes 4xx and 5xx) over time. This metric highlights potential issues in the API.
- **PromQL Query:** `sum(rate(http_requests_total{http_status!~"2.."}[1m])) / sum(rate(http_requests_total[1m]))`
- **Use:** Monitoring this panel helps identify problematic areas in the API that may need attention, reducing the likelihood of errors affecting users.

### Panel 3: Total Time Out Requests

- **Description:** Indicates the number of requests that have timed out. A high timeout rate can signal issues with the API’s responsiveness or network problems.
- **PromQL Query:** `sum(http_request_timeout_total)`
- **Use:** This panel is important for identifying and addressing issues related to slow response times or network instability.

  ![ss-fail](https://github.com/user-attachments/assets/89256e3e-c0d9-403c-b36d-8d1dd68d8b10)

## Errors

### Panel 1: HTTP Errors 4xx

- **Description:** Tracks the number of client-side errors (HTTP status code 4xx) returned by the API. It indicates user-related issues such as bad requests.
- **PromQL Query:** `sum(increase(http_request_failures_total{http_status=~"4.."}[1m])) by (method, endpoint)`
- **Use:** This metric is useful for identifying common client-side errors, allowing for improvements in request validation and user guidance.

### Panel 2: HTTP Errors 5xx

- **Description:** Measures the number of server-side errors (HTTP status code 5xx) encountered by the API. It points to issues within the server or application logic.
- **PromQL Query:** `sum(increase(http_request_failures_total{http_status=~"5.."}[1m])) by (method, endpoint)`
- **Use:** Monitoring this panel is crucial for detecting and resolving server-side problems that could lead to downtime or degraded performance.

  ![errors](https://github.com/user-attachments/assets/baa91cbb-5215-4f9b-9449-93065c4a68a8)

## Requests Rate

### Panel 1: Requests Rate

- **Description:** Displays the rate of incoming HTTP requests to the API separated by http method and endpoint. This metric is essential for understanding the traffic pattern and load on the API.
- **PromQL Query:** `sum(rate(http_requests_total[1m])) by (method, endpoint)`
- **Use:** This metric helps to understand which parts of the API are being used the most and how, allowing to see if a specific endpoint is receiving many requests or if a particular method (such as POST) is being used more than others. This is useful for identifying usage patterns and for detecting if any particular endpoint is being overloaded.

  ![rqsrate](https://github.com/user-attachments/assets/10af7eab-5027-472e-b2fa-f0256c57371e)

## Latency

### Panel 1: Average Latency

- **Description:** Shows the average latency of HTTP requests, measured in seconds. It reflects the time taken by the API to process requests.
- **PromQL Query:** `rate(http_request_latency_seconds_sum[1m]) / rate(http_request_latency_seconds_count[1m])`
- **Use:** This panel is essential for assessing the responsiveness of the API and ensuring that it meets performance expectations.

### Panel 2: Request Latency

- **Description:** Provides a histogram of request latencies, helping to identify the distribution of response times across different requests.
- **PromQL Query:** `histogram_quantile(0.50, sum(rate(http_request_latency_histogram_seconds_bucket[1m])) by (le, method, endpoint))` (and the same x2 times more but with 0.90 and 0.95 percentiles).
- **Use:** This metric is valuable for identifying and optimizing the slowest 5% of requests, improving the overall user experience.

![latency](https://github.com/user-attachments/assets/97d7cb0f-780c-4ada-81cd-a2741f277957)
