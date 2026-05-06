# AGENTS.md

このリポジトリは ZMK Studio RPC の protobuf schema を管理する fork です。
Meteorite editor / ZMK fork / TS client は、この repository の `.proto` を正本として扱います。

## 作業方針

- `.proto` の field number / oneof tag / subsystem tag は互換性に直結します。既存 tag の再利用や意味変更はしないでください。
- 後方互換が必要な追加は、既存 message の末尾に新しい field を追加する形を優先します。
- message 名や enum 名を変更する場合は、ZMK firmware の nanopb 生成名と TS client の生成名の両方を確認してください。
- `upstream` remote は `zmkfirmware/zmk-studio-messages` を指す追従元として維持します。
- 現行の Meteorite Studio 連携 branch は `feat/meteorite-custom-config-rpc` です。ZMK fork と TS client fork はこの branch の schema を参照します。
- upstream が新しい Studio subsystem tag を追加した場合、Meteorite fork の tag と衝突しないかを最初に確認します。

## Branch / Fork 運用

- `origin` は Meteorite fork (`iwk7273/zmk-studio-messages`)、`upstream` は `zmkfirmware/zmk-studio-messages` として扱います。
- Meteorite schema の正本 branch は `feat/meteorite-custom-config-rpc` です。
- branch 名や commit を変えたら、`zmk/app/west.yml` の `zmk-studio-messages` revision と `zmk-studio-ts-client` の submodule pointer を更新してください。
- upstream に同じ subsystem / field が入った場合は、tag 衝突を解消してから firmware / TS client / editor を更新します。

## Meteorite 拡張

- `proto/zmk/meteorite.proto` を追加し、`studio.proto` の subsystem tag `6` として接続しています。
- `core.GetDeviceInfoResponse.capabilities` は optional capability gate です。Meteorite 対応 firmware は `meteorite.config` を返し、editor はそれがある場合だけ Meteorite RPC を呼びます。
- `ConfigState` は custom config の schema / current / saved / defaults / dirty をまとめて返します。
- `ConfigState.encoder_slots` は rotary encoder の keymap position metadata です。editor が physical layout sentinel や末尾 key 数を推測しなくて済むよう、firmware から正本を渡します。

## 更新手順

- schema を変更したら、`zmk-studio-ts-client` の submodule をこの repo の該当 commit に更新し、`npm run generate` と `npm run build` を実行します。
- ZMK firmware 側では nanopb 生成名が変わるため、`zmk/app/src/studio/*` の compile を確認します。
- editor 側は古い firmware / 古い schema でも接続不能にならないよう、capability gate と optional field fallback を維持します。

## 編集境界

- 生成済み TS や firmware の生成 header はこの repo では管理しません。
- protocol の説明を README に追加してもよいですが、詳細な editor UI 仕様は editor repo の `IMPLEMENTATION.md` を正本にします。
