# joopic_ctrl

From com.joobot.controller.CambuddyController, the possible downward control flow may consist the following parts:
```java
public class CamBuddyController implements ActionCtrlr, ConfigCtrlr, PhotoAccessCtrlr, StateCtrlr, Controller {
    private static final int BATTERY_PERIOD = 60000;
    private static final int SOCKET_CLOSE = 0;
    private static final int SOCKET_OPEN = 1;
    public static final int WS_STATE_CLOSE = 2;
    public static final int WS_STATE_OPEN = 1;
    private int commandStatus;
    private TimerTask mBatteryTask;
    private Timer mBatteryTimer;
    private OnSocketStateChangeListener mStateChangeListener;
    private WebSocketConnection mWSConnection;
    private WebSocketClient mWScoketClient;
    public static String IP = "192.168.8.1";
    public static String PORT = "8090";
    public static String COMMON_URL = "ws://" + IP + ":" + PORT + "/dslrcontrol";
    private final WebSocketHandler mWebSocketHandler = new MyWebSocketHandler();
    private CamBuddyCallback callback = CamBuddyCallback.getInstance();
    private MyLogger log = MyLogger.getLogger(CamBuddyController.class.getSimpleName());
    private boolean mUseJavaWebSocket = false;
    private Handler mHandler = new Handler();
```
```java
    public void init() {
        if (this.mUseJavaWebSocket) {
            this.mWScoketClient = new MyWebSocketClient(URI.create(COMMON_URL));
        } else {
            this.mWSConnection = new WebSocketConnection();
        }
    }
```
```java
private String mapToJsonString(Map<String, Object> obj) {
        try {
            StringBuilder builder = new StringBuilder().append("{");
            int i = 0;
            for (Map.Entry<String, Object> entry : obj.entrySet()) {
                if (i > 0) {
                    builder.append(",");
                }
                builder.append("\"").append(entry.getKey()).append("\":");
                if (Integer.class.isInstance(entry.getValue())) {
                    builder.append(entry.getValue());
                } else if (Long.class.isInstance(entry.getValue())) {
                    builder.append(entry.getValue());
                } else if (Float.class.isInstance(entry.getValue())) {
                    builder.append(entry.getValue());
                } else if (Map.class.isInstance(entry.getValue())) {
                    builder.append(mapToJsonString((Map) entry.getValue()));
                } else if (ArrayList.class.isInstance(entry.getValue())) {
                    builder.append(arrayListToJsonString((ArrayList) entry.getValue()));
                } else {
                    builder.append("\"").append(entry.getValue()).append("\"");
                }
                i++;
            }
            builder.append(h.d);
            String jsonStr = builder.toString();
            if (jsonStr == null) {
            }
            return jsonStr;
        } catch (Exception e) {
            throw new RuntimeException("Object would be serialized to `null`");
        }
    }
```

And from com.joobot.joopic.controller.impl.support.cmds, the available commands are as follows:
```java
public class Cmds {
    public static final String CMD_ID_APS_INFO = "apsinfo";
    public static final String CMD_ID_AP_SETTING = "apsetting";
    public static final String CMD_ID_BATTERY_LEVEL = "batterylevel";
    public static final String CMD_ID_BIND = "bind";
    public static final String CMD_ID_BLUETOOTH = "bluetooth";
    public static final String CMD_ID_BULBSHOOT = "bulbshoot";
    public static final String CMD_ID_CAMERA = "camera";
    public static final String CMD_ID_CONTROLLER_INFO = "controllerinfo";
    public static final String CMD_ID_DATETIME = "datetime";
    public static final String CMD_ID_DELETE_PHOTO = "deleteimage";
    public static final String CMD_ID_DISLVUDP = "dislvudp";
    public static final String CMD_ID_ENV_LIGHT = "envlight";
    public static final String CMD_ID_ENV_SOUND = "envsound";
    public static final String CMD_ID_FILELIST = "filelist";
    public static final String CMD_ID_FOCUS = "focus";
    public static final String CMD_ID_FOCUS_FRAME = "focusframe";
    public static final String CMD_ID_FOCUS_MODE = "focusmode";
    public static final String CMD_ID_FOCUS_RING = "focusring";
    public static final String CMD_ID_FOLDERS = "folders";
    public static final String CMD_ID_FTPUPLOAD = "ftpupload";
    public static final String CMD_ID_GETINITSTATE = "getinitstate";
    public static final String CMD_ID_GET_EXIF = "getexif";
    public static final String CMD_ID_GET_PROPERTY = "getproperty";
    public static final String CMD_ID_GET_RADIO_FREQUENCY = "radiofreq";
    public static final String CMD_ID_GET_SHOOT_PARAMS = "getconfig";
    public static final String CMD_ID_INITSTATE = "initstate";
    public static final String CMD_ID_LASER = "laser";
    public static final String CMD_ID_LIGHTING = "lighting";
    public static final String CMD_ID_LIVE_VIEW = "liveview";
    public static final String CMD_ID_LVUDPINFO = "lvudpinfo";
    public static final String CMD_ID_MANUALRADIO = "manualradio";
    public static final String CMD_ID_MCUUPDATE = "mcuupdate";
    public static final String CMD_ID_MCU_EXIT_UPDATE = "mcu_exit_update";
    public static final String CMD_ID_MCU_VERSION = "mcuversion";
    public static final String CMD_ID_MODECONFIG = "modeconfig";
    public static final String CMD_ID_MODEL = "model";
    public static final String CMD_ID_NET_RESTART = "nr";
    public static final String CMD_ID_NOTIFICATION = "notification";
    public static final String CMD_ID_PHOTO_LIST = "imagelist";
    public static final String CMD_ID_PUSH_BACK = "pushback";
    public static final String CMD_ID_RADIO = "radio";
    public static final String CMD_ID_REGULAR_SHOOT = "intervalshoot";
    public static final String CMD_ID_RESTORE_MODE = "restoremode";
    public static final String CMD_ID_SETTINGS = "settings";
    public static final String CMD_ID_SET_PROPERTY = "setproperty";
    public static final String CMD_ID_SET_RADIO_FREQUENCY = "setradiofreq";
    public static final String CMD_ID_SET_SHOOT_PARAMS = "setconfig";
    public static final String CMD_ID_SHOOT = "shoot";
    public static final String CMD_ID_SOUND = "sound";
    public static final String CMD_ID_STATE = "state";
    public static final String CMD_ID_STORAGE_INFO = "storageinfo";
    public static final String CMD_ID_TRIM_FOCUS = "trimfocus";
    public static final String CMD_ID_VIDEO = "video";
    public static final String CMD_ID_ZOOM = "zoom";
}
```

Futhermore, here is the handshake part:
```java
private void sendClientHandshake(WebSocketMessage.ClientHandshake message) throws IOException {
        String path;
        if (message.mQuery != null) {
            path = String.valueOf(message.mPath) + "?" + message.mQuery;
        } else {
            path = message.mPath;
        }
        this.mBuffer.write("GET " + path + " HTTP/1.1");
        this.mBuffer.crlf();
        this.mBuffer.write("Host: " + message.mHost);
        this.mBuffer.crlf();
        this.mBuffer.write("Upgrade: WebSocket");
        this.mBuffer.crlf();
        this.mBuffer.write("Connection: Upgrade");
        this.mBuffer.crlf();
        this.mBuffer.write("Sec-WebSocket-Key: " + newHandshakeKey());
        this.mBuffer.crlf();
        if (message.mOrigin != null && !message.mOrigin.equals("")) {
            this.mBuffer.write("Origin: " + message.mOrigin);
            this.mBuffer.crlf();
        }
        if (message.mSubprotocols != null && message.mSubprotocols.length > 0) {
            this.mBuffer.write("Sec-WebSocket-Protocol: ");
            for (int i = 0; i < message.mSubprotocols.length; i++) {
                this.mBuffer.write(message.mSubprotocols[i]);
                this.mBuffer.write(", ");
            }
            this.mBuffer.crlf();
        }
        this.mBuffer.write("Sec-WebSocket-Version: 13");
        this.mBuffer.crlf();
        this.mBuffer.crlf();
    }
```
```java
private String newHandshakeKey() {
        byte[] ba = new byte[16];
        this.mRng.nextBytes(ba);
        return Base64.encodeToString(ba, 2);
    }
```

Concerin the background handlers, here is some information:
```java
private class WebsocketWriteThread implements Runnable {
        private WebsocketWriteThread() {
        }

        @Override // java.lang.Runnable
        public void run() {
            Thread.currentThread().setName("WebsocketWriteThread");
            while (!Thread.interrupted()) {
                try {
                    ByteBuffer buffer = WebSocketClient.this.engine.outQueue.take();
                    Log.i("xsm", "##########################write.begin.....................");
                    WebSocketClient.this.ostream.write(buffer.array(), 0, buffer.limit());
                    Log.i("xsm", "..........................write.end begin flush #####################");
                    WebSocketClient.this.ostream.flush();
                    Log.i("xsm", "..........................write.end end flush #####################");
                } catch (IOException e) {
                    WebSocketClient.this.engine.eot();
                    return;
                } catch (InterruptedException e2) {
                    return;
                }
            }
        }
    }
```

```java
public void run() {
        int readBytes;
        try {
            if (this.socket == null) {
                this.socket = new Socket(this.proxy);
            } else if (this.socket.isClosed()) {
                throw new IOException();
            }
            this.socket.setTcpNoDelay(true);
            this.socket.setKeepAlive(true);
            this.socket.setOOBInline(true);
            this.socket.setSendBufferSize(1024);
            this.socket.setReceiveBufferSize(1024);
            if (!this.socket.isBound()) {
                this.socket.connect(new InetSocketAddress(this.uri.getHost(), getPort()), this.connectTimeout);
            }
            this.istream = this.socket.getInputStream();
            this.ostream = this.socket.getOutputStream();
            sendHandshake();
            this.writeThread = new Thread(new WebsocketWriteThread());
            this.writeThread.start();
            byte[] rawbuffer = new byte[WebSocketImpl.RCVBUF];
            while (!isClosed() && (readBytes = this.istream.read(rawbuffer)) != -1) {
                try {
                    Log.i("xsm", "............................................... read message.....begin");
                    this.engine.decode(ByteBuffer.wrap(rawbuffer, 0, readBytes));
                    Log.i("xsm", "............................................... read message.....end");
                } catch (IOException e) {
                    this.engine.eot();
                } catch (RuntimeException e2) {
                    onError(e2);
                    this.engine.closeConnection(1006, e2.getMessage());
                }
            }
            this.engine.eot();
            if (!$assertionsDisabled && !this.socket.isClosed()) {
                throw new AssertionError();
            }
        } catch (Exception e3) {
            onWebsocketError(this.engine, e3);
            this.engine.closeConnection(-1, e3.getMessage());
        }
    }
```
