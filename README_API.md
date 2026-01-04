# ESP32 API Data Fetching Examples

This directory contains multiple ESPHome configuration files demonstrating how to fetch data from APIs and display it on an SSD1306 OLED screen.

## Configuration Files

### 1. `esp32c_mini_api_weather.yaml`
**Purpose:** Fetch and display weather data from Open-Meteo API

**Features:**
- Fetches weather data for Ho Chi Minh City
- Updates every 5 minutes
- Displays temperature and wind speed
- Uses template sensors for parsed data

**API Endpoint:** `https://api.open-meteo.com/v1/forecast?latitude=10.8231&longitude=106.6297&current_weather=true`

**Static IP:** `192.168.2.32`

### 2. `esp32c_mini_api_custom.yaml`
**Purpose:** Fetch data from custom API endpoints with status monitoring

**Features:**
- Multiple API endpoint support
- Text wrapping for long responses
- Online/Offline status indicator
- Manual refresh switch
- Displays up to 42 characters across 2 lines

**Example APIs:**
- GitHub Zen API (motivational quotes)
- HTTPBin status endpoint

**Static IP:** `192.168.2.33`

### 3. `esp32c_mini_api_json.yaml`
**Purpose:** Fetch and parse JSON data (Bitcoin prices example)

**Features:**
- JSON response parsing
- Displays Bitcoin prices in USD and EUR
- Updates every 30 seconds
- Debug logging enabled
- Live/Paused status based on switch

**API Endpoint:** `https://api.coindesk.com/v1/bpi/currentprice/BTC.json`

**Static IP:** `192.168.2.34`

## Hardware Requirements

- ESP32-C3 DevKit M-1 board
- SSD1306 OLED Display (128x64, I2C)
- Connections:
  - SDA: GPIO5
  - SCL: GPIO6
  - Switch: GPIO10

## How to Use

### 1. Choose a Configuration
Select the configuration file that matches your needs:
- Weather data → `esp32c_mini_api_weather.yaml`
- Custom API → `esp32c_mini_api_custom.yaml`
- JSON parsing → `esp32c_mini_api_json.yaml`

### 2. Customize the API Endpoint
Edit the `url` parameter in the configuration file to point to your API:

```yaml
text_sensor:
  - platform: http_request
    name: "API Response"
    id: api_response
    url: "https://your-api-endpoint.com/data"  # Change this
    update_interval: 60s
```

### 3. Flash to ESP32
```bash
esphome run esp32c_mini_api_weather.yaml
```

### 4. Monitor Logs
```bash
esphome logs esp32c_mini_api_weather.yaml
```

## Customization Guide

### Change Update Interval
Modify the `update_interval` parameter:
```yaml
update_interval: 60s  # Update every 60 seconds
```

### Add HTTP Headers
Add authentication or custom headers:
```yaml
http_request:
  useragent: esphome/device
  timeout: 10s
  headers:
    Authorization: "Bearer YOUR_TOKEN"
    Content-Type: "application/json"
```

### Parse JSON Data
Use lambda functions to parse JSON responses:
```yaml
on_response:
  then:
    - lambda: |-
        // Parse JSON and extract values
        DynamicJsonDocument doc(1024);
        deserializeJson(doc, body);
        
        // Extract specific fields
        const char* value = doc["field_name"];
        id(my_sensor).publish_state(value);
```

### Display Customization
Modify the `display` lambda to change layout:
```yaml
lambda: |-
  // Custom header
  it.print(64, 0, id(font_large), TextAlign::TOP_CENTER, "MY TITLE");
  
  // Add your content
  it.printf(0, 20, id(font_small), "Data: %s", id(my_data).state.c_str());
```

## API Examples You Can Use

### 1. Weather APIs
- **Open-Meteo:** `https://api.open-meteo.com/v1/forecast?latitude=LAT&longitude=LON&current_weather=true`
- **OpenWeatherMap:** `https://api.openweathermap.org/data/2.5/weather?q=CITY&appid=API_KEY`

### 2. Cryptocurrency
- **CoinDesk:** `https://api.coindesk.com/v1/bpi/currentprice/BTC.json`
- **CoinGecko:** `https://api.coingecko.com/api/v3/simple/price?ids=bitcoin&vs_currencies=usd`

### 3. Time/Date
- **WorldTimeAPI:** `http://worldtimeapi.org/api/timezone/Asia/Ho_Chi_Minh`

### 4. Random Data
- **GitHub Zen:** `https://api.github.com/zen`
- **Random Quote:** `https://api.quotable.io/random`
- **Random Joke:** `https://official-joke-api.appspot.com/random_joke`

### 5. IoT/Home Automation
- **Home Assistant:** `http://YOUR_HA_IP:8123/api/states/sensor.YOUR_SENSOR`
- **Custom Backend:** Your own API endpoints

## Troubleshooting

### API Not Responding
1. Check internet connection
2. Verify API endpoint is accessible
3. Check firewall settings
4. Increase timeout value

### Display Shows "Loading..."
1. Check I2C connections (SDA, SCL)
2. Verify display address (0x3C)
3. Check logs for errors

### JSON Parsing Errors
1. Enable debug logging: `logger: level: DEBUG`
2. Verify JSON structure matches parsing code
3. Check API response format

### Memory Issues
1. Reduce update frequency
2. Limit response size
3. Use smaller fonts
4. Decrease buffer sizes

## Network Configuration

All configurations use the same WiFi settings:
- **SSID:** DuyLongNetwork
- **Password:** duylongpass
- **Gateway:** 192.168.2.1
- **Subnet:** 255.255.255.0

Static IPs assigned:
- Weather: `192.168.2.32`
- Custom API: `192.168.2.33`
- JSON Parser: `192.168.2.34`

## Security Notes

⚠️ **Important Security Considerations:**

1. **API Keys:** Never commit API keys to version control
2. **Passwords:** Change default OTA and encryption passwords
3. **WiFi:** Use WPA2/WPA3 encryption
4. **HTTPS:** Prefer HTTPS endpoints when available
5. **Rate Limiting:** Respect API rate limits

## Next Steps

1. **Customize for your API:** Replace example URLs with your own endpoints
2. **Add authentication:** Include API keys or tokens as needed
3. **Parse specific data:** Extract and display relevant fields from JSON
4. **Add error handling:** Implement fallbacks for failed requests
5. **Create dashboards:** Use Home Assistant integration for advanced monitoring

## Resources

- [ESPHome Documentation](https://esphome.io/)
- [HTTP Request Component](https://esphome.io/components/http_request.html)
- [SSD1306 Display](https://esphome.io/components/display/ssd1306.html)
- [Text Sensor](https://esphome.io/components/text_sensor/index.html)

## License

These configuration files are provided as examples for educational purposes.
