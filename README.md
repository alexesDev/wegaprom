## Как использоовать плейбук

- прописать свой ип в hosts
- прописать ип или домен в site.yaml (main_domain)
- запустить ansible-playbook site.yml

## Доступ к grafana

Пароль и логин по-умолчанию: admin/admin

## Доступ к push gateway

Пароль и логин от push gateway: esp/F8aZ3zFcQDShpd33

Новые можно сгенерировать командой:
```
htpasswd -nb user password
```
и записать результат в site.yaml:
```
traefik.http.middlewares.auth.basicauth.users: "xxx"
```

## Пример отправки метрик с esp32

```cpp
WiFiClient client;
HTTPClient metricsHttp;
string metrics;

String gauge(String name, String value) {
  String val = String("# TYPE ") + name + " gauge\n";
  val += name + " " + value + "\n";
  return val;
}

void init() {
  metricsHttp.begin(client, "http://xxx/metrics/job/box/instance/terrace_box");
  metricsHttp.setAuthorization("esp", "F8aZ3zFcQDShpd33");
}

String fFTS(float x, byte precision) {
  char tmp[50];
  dtostrf(x, 0, precision, tmp);
  return String(tmp);
}

void sendMetrics(void *pvParameters) {
  metrics = "";
  metrics += gauge("wegabox_root_temp", fFTS(rootTemp.temperature, 3));
  metrics += gauge("wegabox_air_temp", fFTS(airTemp.temperature, 3));

  metricsHttp.POST(metrics);
}
```
