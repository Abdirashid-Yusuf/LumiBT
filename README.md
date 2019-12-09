# LumiBT

Introduction

Bluetooth is a way to send and receive data between two different devices. Android platform includes support for the Bluetooth framework that allows devices to wirelessly exchange data with other Bluetooth devices. We can enable Bluetooth using the Bluetooth adapater class
```
BluetoothAdapter adapter = BluetoothAdapter.getDefaultAdapter();
adapter.enable();

```
For this to happen, you need to add extra permisions in the AndroidManifest.xml
```

<uses-permission android:name="android.permission.BLUETOOTH" />
<uses-permission android:name="android.permisson.BLUETOOTH"/>

``` 
History







Major methods & Attributes
i)	BroadcastReicvers
I have used different methods throughout my project. Broadcast receivers have come in hand to catch the state change and log them. The first one is for ACTION_FOUND.
```

private final BroadcastReceiver mBroadcastReciever1  = new BroadcastReceiver() {
    public void onReceive(Context context, Intent intent) {
        String action = intent.getAction();
        //when descovery finds device
        if (action.equals(BluetoothAdapter.ACTION_STATE_CHANGED)) {
            final int state = intent.getIntExtra(BluetoothAdapter.EXTRA_STATE, BluetoothAdapter.ERROR);
            switch (state){
                case BluetoothAdapter.STATE_OFF:
                    Log.d(TAG,"onReceive: STATE OFF");
                    break;
                case BluetoothAdapter.STATE_TURNING_OFF:
                    Log.d(TAG, "mBroadcastReceiver1: STATE TURNING OFF");
                    break;
                case BluetoothAdapter.STATE_ON:
                    Log.d(TAG, "mBroadcastReceiver1: STATE ON");
                    break;
                case BluetoothAdapter.STATE_TURNING_ON:
                    Log.d(TAG, "mBroadcastReceiver1: STATE TURNING ON");
                    break;

            }


        }
    }
};

``` 
This is followed by mBroadcastReciever2 which catches and logs statechange i.e. Discoverability.
```
private final BroadcastReceiver mBroadcastReciever2 = new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, Intent intent) {
            final String action = intent.getAction();
            if (action.equals(BluetoothAdapter.ACTION_SCAN_MODE_CHANGED)) {
                int mode = intent.getIntExtra(BluetoothAdapter.EXTRA_SCAN_MODE, BluetoothAdapter.ERROR);
                switch (mode) {
//Device is in descoverable mode
                    case BluetoothAdapter.SCAN_MODE_CONNECTABLE_DISCOVERABLE:
                        Log.d(TAG, "mBroadcastReceiver2: Discoverability Enabled.");
                    break;
                  //Device not in discoverable mode
                    case BluetoothAdapter.SCAN_MODE_CONNECTABLE:
                        Log.d(TAG, "mBroadcastReceiver2: Discoverability Disabled. Able to receive connections.");
                        break;

                    case BluetoothAdapter.SCAN_MODE_NONE:
                        Log.d(TAG, "mBroadcastReceiver2: Discoverability Disabled. Not able to receive connections.");

                        break;
                    case BluetoothAdapter.STATE_CONNECTING:
                        Log.d(TAG, "mBroadcastReceiver2: Connecting....");
                        break;
                    case BluetoothAdapter.STATE_CONNECTED:
                        Log.d(TAG, "mBroadcastReceiver2: Connected.");
                        break;
                }
            }
        }
    };
```

Thirdly, mBroadcast3 I have usedBroadcastReceiver method for listing devices that are not yet paired
 ```
private BroadcastReceiver mBroadcastReceiver3 = new BroadcastReceiver() {
    @Override
    public void onReceive(Context context, Intent intent) {
        final String action = intent.getAction();
        Log.d(TAG, "onReceive: ACTION FOUND.");
        if (action.equals(BluetoothDevice.ACTION_FOUND)){
            BluetoothDevice device = intent.getParcelableExtra (BluetoothDevice.EXTRA_DEVICE);
            mBTDevices.add(device);
            Log.d(TAG, "onReceive: " + device.getName() + ": " + device.getAddress());
           mDeviceListAdapter = new DeviceListAdapter(context, R.layout.device_adapter_view, mBTDevices);
           // mDeviceListAdapter = new DeviceListAdapter(context, R.layout.device_adapter_view);
            lvNewDevices.setAdapter(mDeviceListAdapter);
        }


    }
};


```
This method is executed by btnDiscover() method


```
public void btnDiscover(View view) {
    Log.d(TAG, "btnDiscover: Looking for unpaired devices.");
    if (mBlutoothadapter.isDiscovering()){
        mBlutoothadapter.cancelDiscovery();
        Log.d(TAG, "btnDiscover: Canceling discovery.");

        //check BT permissions in manifest
        checkBTPermissions();

        mBlutoothadapter.startDiscovery();
        IntentFilter discoverDevicesIntent = new IntentFilter(BluetoothDevice.ACTION_FOUND);
        registerReceiver(mBroadcastReceiver3, discoverDevicesIntent);
    }

    if(!mBlutoothadapter.isDiscovering()){

        //check BT permissions in manifest
        checkBTPermissions();

        mBlutoothadapter.startDiscovery();
        IntentFilter discoverDevicesIntent = new IntentFilter(BluetoothDevice.ACTION_FOUND);
        registerReceiver(mBroadcastReceiver3, discoverDevicesIntent);
    }

}


```

The final Broadcast Reciever that I have used is for detecting bond state changes ( pairing state changes)
```
private final BroadcastReceiver mBroadcastReceiver4 = new BroadcastReceiver() {
    @Override
    public void onReceive(Context context, Intent intent) {
        final String action = intent.getAction();

        if (action.equals(BluetoothDevice.ACTION_BOND_STATE_CHANGED)) {
            BluetoothDevice mDevice = intent.getParcelableExtra(BluetoothDevice.EXTRA_DEVICE);
            //3 cases:
            //case1: bonded already
            if (mDevice.getBondState() == BluetoothDevice.BOND_BONDED) {
                Log.d(TAG, "BroadcastReceiver: BOND_BONDED.");
            }
            //case2: creating a bone
            if (mDevice.getBondState() == BluetoothDevice.BOND_BONDING) {
                Log.d(TAG, "BroadcastReceiver: BOND_BONDING.");
            }
            //case3: breaking a bond
            if (mDevice.getBondState() == BluetoothDevice.BOND_NONE) {
                Log.d(TAG, "BroadcastReceiver: BOND_NONE.");
            }
        }
    }
};

```

ii)	Public Methods

public void enabledisable()























