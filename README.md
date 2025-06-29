# VM Maker | Revolutionary IoT API Solution for ESP32

## ESP32 API Server

This section details the API server workflows for ESP32 devices across different regions and use cases, including primary systems, backup systems, failover mechanisms, and detailed error handling.

### TR (Kendi Sistemleri için - api.vmmaker.com.tr)
This flow represents the request control process for user systems using `api.vmmaker.com.tr`.

```mermaid
sequenceDiagram
    participant Client
    participant Primary_API_TR
    participant Auth_TR
    participant Primary_Database_TR
    participant Backup_API_TR
    participant Backup_Database_TR

    Client->>Primary_API_TR: HTTP Request to api.vmmaker.com.tr/v1/[istekkonrolü]
    Primary_API_TR->>Auth_TR: Validate JWT/Token
    Auth_TR-->>Primary_API_TR: Validation Result
    alt Success
        Primary_API_TR->>Primary_Database_TR: Query [istekkonrolü] data
        Primary_Database_TR-->>Primary_API_TR: Data Response
        Primary_API_TR-->>Client: Success Response with Data
    else Failure
        Primary_API_TR->>Backup_API_TR: Failover Request
        Backup_API_TR->>Backup_Database_TR: Query [istekkonrolü] data
        Backup_Database_TR-->>Backup_API_TR: Data Response
        Backup_API_TR-->>Client: Success Response with Data
        Backup_API_TR-->>Primary_API_TR: Notify Failover
    end
    alt Database Error
        Primary_API_TR-->>Client: Error Response (500)
        Primary_API_TR->>Backup_API_TR: Trigger Failover
        Backup_API_TR-->>Client: Redirect to Backup (302)
    end
```

- **Details**: Primary API (`Primary_API_TR`) handles requests with JWT validation. If primary fails, `Backup_API_TR` takes over with synchronized `Backup_Database_TR`. Failover triggers a 302 redirect.

---

### TR (ESP/Sistem için - api-tr.vmmaker.com/v1/[istekkonrolü])
This flow represents the request control process for ESP32 devices using `api-tr.vmmaker.com`.

```mermaid
sequenceDiagram
    participant ESP_Device
    participant Primary_API_TR
    participant Auth_TR
    participant Primary_Database_TR
    participant Backup_API_TR
    participant Backup_Database_TR

    ESP_Device->>Primary_API_TR: HTTP/MQTT Request to api-tr.vmmaker.com/v1/[istekkonrolü]
    Primary_API_TR->>Auth_TR: Validate API Key
    Auth_TR-->>Primary_API_TR: Validation Result
    alt Success
        Primary_API_TR->>Primary_Database_TR: Process [istekkonrolü] request
        Primary_Database_TR-->>Primary_API_TR: Processed Data
        Primary_API_TR-->>ESP_Device: Command/Data Response
    else Failure
        Primary_API_TR->>Backup_API_TR: Failover Request
        Backup_API_TR->>Backup_Database_TR: Process [istekkonrolü] request
        Backup_Database_TR-->>Backup_API_TR: Processed Data
        Backup_API_TR-->>ESP_Device: Command/Data Response
        Backup_API_TR-->>Primary_API_TR: Notify Failover
    end
    alt Network Error
        Primary_API_TR-->>ESP_Device: Error Response (503)
        Primary_API_TR->>Backup_API_TR: Trigger Failover
        Backup_API_TR-->>ESP_Device: Retry Request
    end
```

- **Details**: ESP devices use API keys for authentication. `Backup_API_TR` provides failover with synchronized data. Network errors trigger a 503 and retry via backup.

---

### TR (ESP/Sistem için - clod-api-tr.vmmaker.com/v1/[istekkonrolü])
This flow represents the cloud-based request control for ESP32 devices using `clod-api-tr.vmmaker.com`.

```mermaid
sequenceDiagram
    participant ESP_Device
    participant Primary_Cloud_API_TR
    participant Auth_TR
    participant Primary_Cloud_Database_TR
    participant Backup_Cloud_API_TR
    participant Backup_Cloud_Database_TR

    ESP_Device->>Primary_Cloud_API_TR: HTTP/WebSocket Request to clod-api-tr.vmmaker.com/v1/[istekkonrolü]
    Primary_Cloud_API_TR->>Auth_TR: Validate API Key
    Auth_TR-->>Primary_Cloud_API_TR: Validation Result
    alt Success
        Primary_Cloud_API_TR->>Primary_Cloud_Database_TR: Fetch/Process [istekkonrolü] data
        Primary_Cloud_Database_TR-->>Primary_Cloud_API_TR: Data Response
        Primary_Cloud_API_TR-->>ESP_Device: Real-time Data/Command
    else Failure
        Primary_Cloud_API_TR->>Backup_Cloud_API_TR: Failover Request
        Backup_Cloud_API_TR->>Backup_Cloud_Database_TR: Fetch/Process [istekkonrolü] data
        Backup_Cloud_Database_TR-->>Backup_Cloud_API_TR: Data Response
        Backup_Cloud_API_TR-->>ESP_Device: Real-time Data/Command
        Backup_Cloud_API_TR-->>Primary_Cloud_API_TR: Notify Failover
    end
    alt Server Overload
        Primary_Cloud_API_TR-->>ESP_Device: Error Response (429)
        Primary_Cloud_API_TR->>Backup_Cloud_API_TR: Load Balance
        Backup_Cloud_API_TR-->>ESP_Device: Distributed Response
    end
```

- **Details**: Cloud API supports real-time WebSocket. `Backup_Cloud_API_TR` handles failover and load balancing with a 429 response for overloads.

---

### FR (ESP/Sistem için - api-fr.vmmaker.com/v1/[istekkonrolü])
This flow represents the request control for ESP32 devices in France using `api-fr.vmmaker.com`.

```mermaid
sequenceDiagram
    participant ESP_Device
    participant Primary_API_FR
    participant Auth_FR
    participant Primary_Database_FR
    participant Backup_API_FR
    participant Backup_Database_FR

    ESP_Device->>Primary_API_FR: HTTP/MQTT Request to api-fr.vmmaker.com/v1/[istekkonrolü]
    Primary_API_FR->>Auth_FR: Validate API Key
    Auth_FR-->>Primary_API_FR: Validation Result
    alt Success
        Primary_API_FR->>Primary_Database_FR: Process [istekkonrolü] request
        Primary_Database_FR-->>Primary_API_FR: Processed Data
        Primary_API_FR-->>ESP_Device: Command/Data Response
    else Failure
        Primary_API_FR->>Backup_API_FR: Failover Request
        Backup_API_FR->>Backup_Database_FR: Process [istekkonrolü] request
        Backup_Database_FR-->>Backup_API_FR: Processed Data
        Backup_API_FR-->>ESP_Device: Command/Data Response
        Backup_API_FR-->>Primary_API_FR: Notify Failover
    end
    alt Timeout
        Primary_API_FR-->>ESP_Device: Error Response (504)
        Primary_API_FR->>Backup_API_FR: Trigger Failover
        Backup_API_FR-->>ESP_Device: Retry with Backup
    end
```

- **Details**: Failover to `Backup_API_FR` on timeout (504). Data synchronization ensures continuity.

---

### FR (ESP/Sistem için - clod-api-fr.vmmaker.com/v1/[istekkonrolü])
This flow represents the cloud-based request control for ESP32 devices in France.

```mermaid
sequenceDiagram
    participant ESP_Device
    participant Primary_Cloud_API_FR
    participant Auth_FR
    participant Primary_Cloud_Database_FR
    participant Backup_Cloud_API_FR
    participant Backup_Cloud_Database_FR

    ESP_Device->>Primary_Cloud_API_FR: HTTP/WebSocket Request to clod-api-fr.vmmaker.com/v1/[istekkonrolü]
    Primary_Cloud_API_FR->>Auth_FR: Validate API Key
    Auth_FR-->>Primary_Cloud_API_FR: Validation Result
    alt Success
        Primary_Cloud_API_FR->>Primary_Cloud_Database_FR: Fetch/Process [istekkonrolü] data
        Primary_Cloud_Database_FR-->>Primary_Cloud_API_FR: Data Response
        Primary_Cloud_API_FR-->>ESP_Device: Real-time Data/Command
    else Failure
        Primary_Cloud_API_FR->>Backup_Cloud_API_FR: Failover Request
        Backup_Cloud_API_FR->>Backup_Cloud_Database_FR: Fetch/Process [istekkonrolü] data
        Backup_Cloud_Database_FR-->>Backup_Cloud_API_FR: Data Response
        Backup_Cloud_API_FR-->>ESP_Device: Real-time Data/Command
        Backup_Cloud_API_FR-->>Primary_Cloud_API_FR: Notify Failover
    end
    alt Data Corruption
        Primary_Cloud_API_FR-->>ESP_Device: Error Response (422)
        Primary_Cloud_API_FR->>Backup_Cloud_API_FR: Restore from Backup
        Backup_Cloud_API_FR-->>ESP_Device: Clean Data Response
    end
```

- **Details**: Handles data corruption with a 422 response and restores from `Backup_Cloud_API_FR`.

---

### USA (ESP/Sistem için - api-usa.vmmaker.com/v1/[istekkonrolü])
This flow represents the request control for ESP32 devices in the USA.

```mermaid
sequenceDiagram
    participant ESP_Device
    participant Primary_API_USA
    participant Auth_USA
    participant Primary_Database_USA
    participant Backup_API_USA
    participant Backup_Database_USA

    ESP_Device->>Primary_API_USA: HTTP/MQTT Request to api-usa.vmmaker.com/v1/[istekkonrolü]
    Primary_API_USA->>Auth_USA: Validate API Key
    Auth_USA-->>Primary_API_USA: Validation Result
    alt Success
        Primary_API_USA->>Primary_Database_USA: Process [istekkonrolü] request
        Primary_Database_USA-->>Primary_API_USA: Processed Data
        Primary_API_USA-->>ESP_Device: Command/Data Response
    else Failure
        Primary_API_USA->>Backup_API_USA: Failover Request
        Backup_API_USA->>Backup_Database_USA: Process [istekkonrolü] request
        Backup_Database_USA-->>Backup_API_USA: Processed Data
        Backup_API_USA-->>ESP_Device: Command/Data Response
        Backup_API_USA-->>Primary_API_USA: Notify Failover
    end
    alt Authentication Server Down
        Primary_API_USA-->>ESP_Device: Error Response (502)
        Primary_API_USA->>Backup_API_USA: Use Cached Auth
        Backup_API_USA-->>ESP_Device: Temporary Response
    end
```

- **Details**: Handles authentication server failure with a 502 and cached authentication via backup.

---

### USA (ESP/Sistem için - clod-api-usa.vmmaker.com/v1/[istekkonrolü])
This flow represents the cloud-based request control for ESP32 devices in the USA.

```mermaid
sequenceDiagram
    participant ESP_Device
    participant Primary_Cloud_API_USA
    participant Auth_USA
    participant Primary_Cloud_Database_USA
    participant Backup_Cloud_API_USA
    participant Backup_Cloud_Database_USA

    ESP_Device->>Primary_Cloud_API_USA: HTTP/WebSocket Request to clod-api-usa.vmmaker.com/v1/[istekkonrolü]
    Primary_Cloud_API_USA->>Auth_USA: Validate API Key
    Auth_USA-->>Primary_Cloud_API_USA: Validation Result
    alt Success
        Primary_Cloud_API_USA->>Primary_Cloud_Database_USA: Fetch/Process [istekkonrolü] data
        Primary_Cloud_Database_USA-->>Primary_Cloud_API_USA: Data Response
        Primary_Cloud_API_USA-->>ESP_Device: Real-time Data/Command
    else Failure
        Primary_Cloud_API_USA->>Backup_Cloud_API_USA: Failover Request
        Backup_Cloud_API_USA->>Backup_Cloud_Database_USA: Fetch/Process [istekkonrolü] data
        Backup_Cloud_Database_USA-->>Backup_Cloud_API_USA: Data Response
        Backup_Cloud_API_USA-->>ESP_Device: Real-time Data/Command
        Backup_Cloud_API_USA-->>Primary_Cloud_API_USA: Notify Failover
    end
    alt Regional Outage
        Primary_Cloud_API_USA-->>ESP_Device: Error Response (503)
        Primary_Cloud_API_USA->>Backup_Cloud_API_USA: Switch Region
        Backup_Cloud_API_USA-->>ESP_Device: Response from Alternate Region
    end
```

- **Details**: Manages regional outages with a 503 and regional switching to `Backup_Cloud_API_USA`.

---

### DE (ESP/Sistem için - api-de.vmmaker.com/v1/[istekkonrolü])
This flow represents the request control for ESP32 devices in Germany.

```mermaid
sequenceDiagram
    participant ESP_Device
    participant Primary_API_DE
    participant Auth_DE
    participant Primary_Database_DE
    participant Backup_API_DE
    participant Backup_Database_DE

    ESP_Device->>Primary_API_DE: HTTP/MQTT Request to api-de.vmmaker.com/v1/[istekkonrolü]
    Primary_API_DE->>Auth_DE: Validate API Key
    Auth_DE-->>Primary_API_DE: Validation Result
    alt Success
        Primary_API_DE->>Primary_Database_DE: Process [istekkonrolü] request
        Primary_Database_DE-->>Primary_API_DE: Processed Data
        Primary_API_DE-->>ESP_Device: Command/Data Response
    else Failure
        Primary_API_DE->>Backup_API_DE: Failover Request
        Backup_API_DE->>Backup_Database_DE: Process [istekkonrolü] request
        Backup_Database_DE-->>Backup_API_DE: Processed Data
        Backup_API_DE-->>ESP_Device: Command/Data Response
        Backup_API_DE-->>Primary_API_DE: Notify Failover
    end
    alt Security Breach
        Primary_API_DE-->>ESP_Device: Error Response (403)
        Primary_API_DE->>Backup_API_DE: Isolate and Switch
        Backup_API_DE-->>ESP_Device: Secure Response
    end
```

- **Details**: Handles security breaches with a 403 and isolates to `Backup_API_DE`.

---

### DE (ESP/Sistem için - clod-api-de.vmmaker.com/v1/[istekkonrolü])
This flow represents the cloud-based request control for ESP32 devices in Germany.

```mermaid
sequenceDiagram
    participant ESP_Device
    participant Primary_Cloud_API_DE
    participant Auth_DE
    participant Primary_Cloud_Database_DE
    participant Backup_Cloud_API_DE
    participant Backup_Cloud_Database_DE

    ESP_Device->>Primary_Cloud_API_DE: HTTP/WebSocket Request to clod-api-de.vmmaker.com/v1/[istekkonrolü]
    Primary_Cloud_API_DE->>Auth_DE: Validate API Key
    Auth_DE-->>Primary_Cloud_API_DE: Validation Result
    alt Success
        Primary_Cloud_API_DE->>Primary_Cloud_Database_DE: Fetch/Process [istekkonrolü] data
        Primary_Cloud_Database_DE-->>Primary_Cloud_API_DE: Data Response
        Primary_Cloud_API_DE-->>ESP_Device: Real-time Data/Command
    else Failure
        Primary_Cloud_API_DE->>Backup_Cloud_API_DE: Failover Request
        Backup_Cloud_API_DE->>Backup_Cloud_Database_DE: Fetch/Process [istekkonrolü] data
        Backup_Cloud_Database_DE-->>Backup_Cloud_API_DE: Data Response
        Backup_Cloud_API_DE-->>ESP_Device: Real-time Data/Command
        Backup_Cloud_API_DE-->>Primary_Cloud_API_DE: Notify Failover
    end
    alt Maintenance Mode
        Primary_Cloud_API_DE-->>ESP_Device: Error Response (503)
        Primary_Cloud_API_DE->>Backup_Cloud_API_DE: Switch to Backup
        Backup_Cloud_API_DE-->>ESP_Device: Maintenance-Free Response
    end
```

- **Details**: Manages maintenance mode with a 503 and switches to `Backup_Cloud_API_DE`.
--
