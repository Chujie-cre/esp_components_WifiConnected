# 通过虾哥的封装修改
# ESP32 Wi‑Fi 连接

该组件封装了esp32的 Wi‑Fi 配网连接流程。

它会先使用存储在闪存中的凭据尝试连接 Wi‑Fi；如果失败，则启动接入点（AP）与 Web 服务器，供用户配置并连接到 Wi‑Fi 网络。

访问 Web 服务器的地址为 `http://192.168.4.1`。

## 截图：Wi‑Fi 配置

![Wi‑Fi 配置](assets/wifi.png)

## 截图：高级选项

![高级配置](assets/ota.png)

- 支持“ota”服务器自定义选项卡。
- 支持 "Connection: close" 响应头以节省打开的套接字。
- 支持语言请求。
- 支持 ESP32 SmartConfig（ESPTouch v2）。
- 支持多 Wi‑Fi SSID 管理。
- 自动切换到最佳 Wi‑Fi 网络。
- 提供用于 Wi‑Fi 配置的强制门户（Captive Portal）。
- 支持多语言（英语、中文）。

## 使用

```cpp
// 初始化默认事件循环
ESP_ERROR_CHECK(esp_event_loop_create_default());

// 初始化 NVS 闪存（用于 Wi‑Fi 配置）
esp_err_t ret = nvs_flash_init();
if (ret == ESP_ERR_NVS_NO_FREE_PAGES || ret == ESP_ERR_NVS_NEW_VERSION_FOUND) {
    ESP_ERROR_CHECK(nvs_flash_erase());
    ret = nvs_flash_init();
}
ESP_ERROR_CHECK(ret);

// 读取 Wi‑Fi 配置
auto& ssid_list = SsidManager::GetInstance().GetSsidList();
if (ssid_list.empty()) {
    // 启动用于配置的 Wi‑Fi AP
    auto& ap = WifiConfigurationAp::GetInstance();
    ap.SetSsidPrefix("ESP32");
    ap.Start();
    return;
}

// 否则，连接到 Wi‑Fi 网络
WifiStation::GetInstance().Start();
```
