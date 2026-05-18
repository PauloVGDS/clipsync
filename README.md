# ClipSync

Umbrella do projeto **ClipSync** - sincronizacao de clipboard entre PC e
celular sob demanda, via ESP32 + TFT touch como gatilho fisico.

Este repo nao tem codigo proprio - ele apenas agrupa os componentes via
git submodules:

| Subpasta     | Repo upstream                                        | Descricao                                      |
|--------------|------------------------------------------------------|------------------------------------------------|
| `firmware/`  | [PauloVGDS/clipsync-firmware][f]                     | ESP32 + ILI9341 + XPT2046 (PlatformIO/Arduino) |
| `pc_client/` | [PauloVGDS/clipsync-pc-client][p]                    | Cliente Python (bleak + pyperclip)             |
| `android/`   | [PauloVGDS/clipsync-android][a]                      | App Android nativo (substitui Tasker)          |

[f]: https://github.com/PauloVGDS/clipsync-firmware
[p]: https://github.com/PauloVGDS/clipsync-pc-client
[a]: https://github.com/PauloVGDS/clipsync-android

## Como clonar tudo de uma vez

    git clone --recursive https://github.com/PauloVGDS/clipsync.git

Se ja clonou sem `--recursive`:

    git clone https://github.com/PauloVGDS/clipsync.git
    cd clipsync
    git submodule update --init --recursive

## Como atualizar os submodules para os ultimos commits

    cd clipsync
    git submodule update --remote --merge
    git commit -am "bump submodules"
    git push

Trabalhar dentro de um submodule e como em qualquer repo:

    cd firmware
    # edita, commita, da push (vai pro repo do submodule)
    cd ..
    git add firmware    # registra na umbrella o novo commit referenciado
    git commit -m "firmware: bump to <hash>"
    git push

## Visao geral da arquitetura

    [ Celular (Tasker/Android) ]  <--BLE-->  [ ESP32 ClipSync ]  <--BLE-->  [ PC (Python) ]
                                                    |
                                     touch UI: PC/Celular + ENVIAR
                                             (gatilho fisico)

Quando o usuario toca **ENVIAR** com **PC** marcado, o ESP32 manda
`cmd=0x01` ao pc_client, que le sua clipboard, faz WRITE em `from_pc`,
o ESP32 relaya em `to_mobile` para o celular gravar.

Detalhes do protocolo BLE e bring-up de hardware: ver README e CLAUDE.md
do firmware.
