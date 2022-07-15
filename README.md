# Nodecord


Nodecord, Cosmos tabanlı bir blockchain doğrulayıcı izleme ve uyarı aracıdır.

Aşağıdakiler gibi senaryolar için izler ve uyarılar:
- Kesinti süresi çalışma süresi
- Son kaçırılan bloklar (şu anda onaylayıcı imzalıyor)
- Hapis durumu
- Mezar taşı durumu
- Bireysel nöbetçi düğümleri erişilemiyor/eşzamansız
- Zincir durduruldu

Aşağıdakiler için yapılandırılmış web kancası kanalında Discord mesajları oluşturulur:
- Geçerli doğrulayıcı durumu
- Algılanan uyarılar

## Kurulum adımları:
### Aşağıdaki otomatik kurulum scripti ile ( Önerilen farklı bir sunucu kullanmanızdır. Bu sayede node'unuzda ve ya sunucunuzda sorun tespit edildiğinde sistem takip edebilir ve size discord üzerinden bildirim gönderebilir.)

```
wget -O NODECORD.sh https://raw.githubusercontent.com/Nodeist/Nodecord/main/NODECORD && chmod +x NODECORD.sh && ./NODECORD.sh
```

## Kurulum sonrası adımlar: 
Nodecord'un doğru şekilde çalışabilmesini sağlamak için için öncelikle bir screen oluşturmalısınız:
```
screen -S Nodecord
```

### Konfigürasyon ayarları:
Bir konfigürasyon dosyası oluşturun.
Bu dosyada DISCORD_WEBHOOK_TOKEN, DISCOR_WEBHOOK_ID, DISCORD_USER_ID kısımlarını düzenlemelisiniz.
Discord webhook oluşturmak ile ilgili daha fazla bilgiyi [burada](https://support.discord.com/hc/en-us/articles/228383668-Intro-to-Webhooks) bulabilirsiniz.

Webhook oluşturduktan sonra url'nizi kopyalayın. şuna benzeyecektir:
`https://discord.com/api/webhooks/97124726447720/cwM4Ks-kWcK3Jsg4I_cbo124buo12G2oıdaS76afsMwuY7elwfnwef-wuuRW`
Bu durumda sizin 
- DISCORD_WEBHOOK_ID: `97124726447720`
- DISCORD_WEBHOOK_TOKEN: `cwM4Ks-kWcK3Jsg4I_cbo124buo12G2oıdaS76afsMwuY7elwfnwef-wuuRW`
Olacaktır.
- DISCORD_USER_ID öğrenme adımını googledan araştırarak kolayca bulabilirsiniz.


Ayrıca hangi node için rapor almak istiyorsanız **validators:** bölümünü de ona göre düzenleyin. 
- Name: Network ismi (Kujira, Osmosis,Quicksilver vs.)
- RPC: RPC hizmeti sunan şirketlerden kolayca RPC bağlantısı bulabilirsiniz. Ben genellikle Polkachu'yu takip ediyorum. 
Kujira için örnek rpc: **https://kujira-rpc.polkachu.com/**
- Adress: Bu ne cüzdan adresiniz ne de valoper adresinizdir. Buna dikkat edin. Consensüs adresiniz olmalı. Explorer'lardan bulabilirsiniz.
- Chain-id: Kujira örneği için **Kaiyo-1**
```
sudo tee ~/Nodecord/config.yaml > /dev/null <<EOF
# Optionally uncomment to ignore specific alerts
#alerts:
#  ignore-alerts:
#    - alertTypeMissedRecentBlocks

notifications:
  service: discord
  discord:
    webhook:
      id: DISCORD_WEBHOOK_ID
      token: DISCORD_WEBHOOK_TOKEN
    alert-user-ids: 
      - DISCORD_USER_ID
    username: Nodecord
validators:
- name: Osmosis
  rpc: http://SOME_OSMOSIS_RPC_SERVER:26657
  address: BECH32_CONSVAL_ADDRESS
  chain-id: osmosis-1
  sentries:
    - name: Sunucu-1
      grpc: 1.2.3.4:9090
  
  EOF
```


See [here](https://support.discord.com/hc/en-us/articles/228383668-Intro-to-Webhooks) for how to create a webhook for a discord channel.

Once you've created the webhook, copy the URL. It'll look something like this: `https://discord.com/api/webhooks/978129125394247720/cwM4Ks-kWcK3Jsg4I_cboauYjOa48ngI2VKaS76afsMwuY7-U4Frw3BGcYXCJvZJ2kWD`

This will be used later to be put into the config.yaml. The webhook id is `978129125394247720` (from the URL), and webhook token is `cwM4Ks-kWcK3Jsg4I_cboauYjOa48ngI2VKaS76afsMwuY7-U4Frw3BGcYXCJvZJ2kWD`

Save the values as follows (note these values are from the URL):
```yml:
webhook:
  id: 978129125394247720
  token: cwM4Ks-kWcK3Jsg4I_cboauYjOa48ngI2VKaS76afsMwuY7-U4Frw3BGcYXCJvZJ2kWD
```

### Start monitoring

Begin monitoring with:

```bash
Nodecord monitor
```

By default, `Nodecord monitor` will look for `config.yaml` in the current working directory. To specify a different config file path, use the `--file`/`-f` flag:

```bash
Nodecord monitor -f ~/config.yaml
```

When a validator is first added to `config.yaml` and Nodecord is started, a status message will be created in the discord channel and the ID of that message will be added to `config.yaml`. Pin this message so that the channel's pinned messages can act as a dashboard to see the realtime status of the validators.

![Screenshot from 2022-02-28 14-29-36](https://user-images.githubusercontent.com/6722152/156061805-330d1c76-acfa-4089-b327-f35f686fa0e7.png)

Alerts will be posted when any error conditions are detected, and follow up messages will be posted when those errors are cleared.

![Screenshot from 2022-02-16 10-53-43](https://user-images.githubusercontent.com/6722152/154326098-12aa787f-389e-4abf-af56-93918090ddc1.png)

For high and critical errors, the configured discord user IDs will be tagged

![Screenshot from 2022-02-16 11-38-00](https://user-images.githubusercontent.com/6722152/154333667-af823075-73fc-4d41-97ce-40432f3450ac.png)

