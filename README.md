# MiBand SDK (traduzido para o Português)



SDK para integração com a pulseira MiBand e MiBand 2 ajustados para o português. Esta SDK é utilizada no projeto Pro-Treino para integração das pulseiras Xiaomi.

[Link do SDK original (não é mais suportado)](https://github.com/pangliang/miband-sdk-android)

[Link do App Pro-Treino no Google Play](https://play.google.com/store/apps/details?id=com.startjob.pro_treino)

[Acesse o site do Pro-Treino para mais informações](https://www.pro-treino.com)

## Release notes

- 1.1.3 (Pro-Treino) 
    - Atualização do gradle (agora usa o 2.3.3)
    - Ajuste no método para coleta de batimentos cardíacos na MiBand 2
- Demais notas verificar branch original

## API

### Escanear e descobrir dispositivos

```java

// instancia singleton
MiBand miband = new MiBand(context);

// procura por dispositivos perto
final ScanCallback scanCallback = new ScanCallback(){
    @Override
    public void onScanResult(int callbackType, ScanResult result){
	    BluetoothDevice device = result.getDevice();
	    Log.d(TAG,"name:" + device.getName() + ",uuid:"
                    	  + device.getUuids() + ",add:”
                    	  + device.getAddress() + ",type:”
                    	  + device.getType() + ",bondState:”
                    	  + device.getBondState() + ",rssi:” + result.getRssi());
        // fazer as tarefas
    }
};

// inicia a procura
MiBand.startScan(scanCallback);

// para a procura
MiBand.stopScan(scanCallback);

```

### Conectar em um disipotivos

```java
// Conecta no dispositivo, só assim é possível enviar comandos.
miband.connect(device, new ActionCallback() {
    @Override
    public void onSuccess(Object data){
        Log.d(TAG,"connect success");
    }
    @Override
    public void onFail(int errorCode, String msg){
        Log.d(TAG,"connect fail, code:"+errorCode+",mgs:"+msg);
    }
});



// defini um listener para quando descobectar do dispositivo
miband.setDisconnectedListener(new NotifyListener(){
    @Override
    public void onNotify(byte[] data){
         Log.d(TAG,"Desconectou!!!”);
	}
});
		
```

### Defini dados do usuário

```java
// Defini dados do usuário
UserInfo userInfo = new UserInfo(20111111, 1, 32, 180, 55, "胖梁", 0);
miband.setUserInfo(userInfo);

```

### Solicita dados de batimentos cardiácos

```java
// defini listener
miband.setHeartRateScanListener(new HeartRateNotifyListener(){
    @Override
    public void onNotify(int heartRate){
        Log.d(TAG, "heart rate: "+ heartRate);
    }
});

// inicia escaneamento, quando pronto, o listener será chamado
miband.startHeartRateScan();

```

### Sinal do RSSI

```java

// 
miband.readRssi(new ActionCallback() {
    @Override
    public void onSuccess(Object data){
        Log.d(TAG, "rssi:"+(int)data);
    }
    @Override
    public void onFail(int errorCode, String msg){
        Log.d(TAG, "readRssi fail");
    }
});

```

### Informações da bateria

```java

// requisita informações da bateria
miband.getBatteryInfo(new ActionCallback() {	
    @Override
    public void onSuccess(Object data){
        BatteryInfo info = (BatteryInfo)data;
        Log.d(TAG, info.toString());
        //cycles:4,level:44,status:unknow,last:2015-04-15 03:37:55
    }
    @Override
    public void onFail(int errorCode, String msg){
        Log.d(TAG, "readRssi fail");
    }
});

```

### Notificações

```java

//vibra 2 vezes com led/tela ativa
miband.startVibration(VibrationMode.VIBRATION_WITH_LED);

//vibra 2 vezes sem led/icone inativa
miband.startVibration(VibrationMode.VIBRATION_WITHOUT_LED);

//vibra 10 vezes com led/tela ativa
miband.startVibration(VibrationMode.VIBRATION_10_TIMES_WITH_LED);

//para a vibração a qualquer momento
miband.stopVibration();

//listener de notificação geral
miband.setNormalNotifyListener(new NotifyListener() {
    @Override
    public void onNotify(byte[] data){
        Log.d(TAG, "NormalNotifyListener:" + Arrays.toString(data));
    }
});

```

```java

// Obter notificação de número de passos em tempo real, configurar, 
// agitar a pulseira (precisa continuar a agitar 10-20),
// receberá notificação em tempo real do número total de etapas 
// Use duas etapas: 

// 1. Configuração de monitoramento Dispositivo
miband.setRealtimeStepsNotifyListener(new RealtimeStepsNotifyListener() {	
    @Override
    public void onNotify(int steps){
        Log.d(TAG, "RealtimeStepsNotifyListener:" + steps);
    }
});

// 2. Ligue a notificação 
miband.enableRealtimeStepsNotify();

// desligar (pausa) notificação de passos em tempo real, 
// para ativar novamente, chame miband.enableRealtimeStepsNotify() 
miband.disableRealtimeStepsNotify();

```

### Iliminação dos LED's (MiBand 1)'

```java
//cor dos leds
miband.setLedColor(LedColor.ORANGE);
miband.setLedColor(LedColor.BLUE);
miband.setLedColor(LedColor.RED);
miband.setLedColor(LedColor.GREEN);
```

### Acelerômetro

```java
// Para obter os dados brutos do sensor de gravidade (acelerometro), faça em dois passos 
// 1. Defina o listener
miband.setSensorDataNotifyListener(new NotifyListener(){
    @Override
    public void onNotify(byte[] data){
        int i = 0;

        int index = (data[i++] & 0xFF) | (data[i++] & 0xFF) << 8;  // número
        int d1 = (data[i++] & 0xFF) | (data[i++] & 0xFF) << 8;    
        int d2 = (data[i++] & 0xFF) | (data[i++] & 0xFF) << 8;
        int d3 = (data[i++] & 0xFF) | (data[i++] & 0xFF) << 8;
    }
});

// 2. habilite as notificações
miband.enableSensorDataNotify();

```

### Emparelhamento
    
Função pouco utilizada, pois não há dados para análise

```java
// emparelhar
miband.pair(new ActionCallback() {
    @Override
    public void onSuccess(Object data){
        changeStatus("pair succ");
    }
    @Override
    public void onFail(int errorCode, String msg){
        changeStatus("pair fail");
    }
});

```
