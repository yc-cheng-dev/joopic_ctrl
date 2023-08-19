# joopic_ctrl

From com.joobot.controller.CambuddyController:
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
