# majsoul-monthTicket-auto

> Warning: Any disadvantages, account sanctions, or other consequences resulting from use of this project are solely the user's responsibility.

This project uses GitHub Actions to log in to Mahjong Soul once per day, collect the monthly ticket reward, and help complete attendance achievements.

## What changed in 2026

Mahjong Soul's JP/EN/KR web client moved to the newer Yostar Account + Unity WebGL flow. The old browser console command below no longer works on the current web client:

```js
console.log(`UID: ${GameMgr.Inst.yostar_uid}\nTOKEN: ${GameMgr.Inst.yostar_accessToken}`);
```

Use Yostar session credentials instead. The script now supports `YOSTAR_UID`, `YOSTAR_TOKEN`, and `YOSTAR_DEVICE_ID`.

## Required secrets

Go to your fork on GitHub, then open `Settings > Secrets and variables > Actions > New repository secret`.

For JP, EN, or KR servers, add:

```text
MS_SERVER=jp
MS_AUTH_MODE=yostar_session
YOSTAR_UID=<Yostar UserInfo.ID>
YOSTAR_TOKEN=<Yostar UserInfo.Token>
YOSTAR_DEVICE_ID=<the same DeviceID used when the token was issued>
```

Set `MS_SERVER` to `jp`, `en`, or `kr`. If omitted, the script defaults to `jp`.

For CN server, add:

```text
MS_SERVER=cn
EMAIL=<account email>
PASSWORD=<account password>
```

## Getting Yostar session credentials

You need the values returned by the Yostar platform API after logging in:

```text
UserInfo.ID      -> YOSTAR_UID
UserInfo.Token   -> YOSTAR_TOKEN
DeviceID         -> YOSTAR_DEVICE_ID
```

The easiest manual path is:

1. Open Mahjong Soul in Chrome and log in with the Yostar account that owns your Mahjong Soul account.
2. Open DevTools with `F12`.
3. Open the `Network` tab.
4. Filter requests by `yostarplat.com`.
5. Look for `/user/login` or `/user/quick-login`.
6. In the request headers, copy the `DeviceID` from the JSON inside the `Authorization` header.
7. In the response body, copy `Data.UserInfo.ID` and `Data.UserInfo.Token`.

Keep these values private. Anyone with these secrets may be able to create a game session for your account.

## GitHub Actions setup

1. Fork this repository.
2. Add the required repository secrets listed above.
3. Go to `Settings > Actions > General`.
4. Set `Workflow permissions` to `Read and write permissions`.
5. Open the `Actions` tab and enable workflows if GitHub asks.
6. Select `Login to Majsoul`.
7. Click `Run workflow` to test manually.

The default scheduled run is `21:05 UTC`, which is `06:05 JST/KST`.

## Troubleshooting

If the workflow fails with `oauth2Auth failed` and `error.code=151`, check both the Yostar OAuth type and the client version. The current web client builds `client_version_string` as `web-` plus `version.json.version` with the trailing `.w` removed, while `client_version.resource` keeps the full `version.json.version`. The Yostar SDK v4 OAuth types are JP `21`, EN `22`, and KR `23`. A JP account using EN type `22` can fail with the same `151` error.

If the workflow fails with a Yostar platform error, refresh your Yostar session credentials and update:

```text
YOSTAR_UID
YOSTAR_TOKEN
YOSTAR_DEVICE_ID
```

If the workflow hangs, make sure the version with WebSocket/RPC timeouts is in your fork. The `Execute month ticket automation` step should fail quickly instead of waiting indefinitely.

If duplicate login disconnects your browser session after a successful test, that is expected. The Action logged in as the same account.

## Optional behavior

The script only collects monthly ticket rewards by default. Green gift buying is disabled in `src/index.js`:

```js
const BUY_GREEN_GIFT = false;
```

## Contact

- [Discord](https://discord.com/users/245702966085025802)
- [X](https://x.com/xflVsSnvB6cx8ZM)
