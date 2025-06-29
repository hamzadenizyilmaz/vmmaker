# VM Maker | Revolutionary IoT API Solution for ESP32

```mermaid
sequenceDiagram
    participant ESP_Device
    participant API_TR
    participant Auth_TR
    participant Database_TR

    ESP_Device->>API_TR: HTTP/MQTT Request to api-tr.vmmaker.com/v1/[istekkonrolü]
    API_TR->>Auth_TR: Validate API Key
    Auth_TR-->>API_TR: Validation Result
    alt Success
        API_TR->>Database_TR: Process [istekkonrolü] request
        Database_TR-->>API_TR: Processed Data
        API_TR-->>ESP_Device: Command/Data Response
    else Failure
        API_TR-->>ESP_Device: Error Response (401/403)
    end
```
