# Android-RFID-NFC-Card-Reader-Integration
This article will guide you how to integrate RFID NFC Card Reader in your android application

## Getting Started
RFID is the process by which items are uniquely identified using radio waves, and NFC is a specialized subset within the family of RFID technology. Specifically, NFC is a branch of High-Frequency (HF) RFID, and both operate at the 13.56 MHz frequency. NFC is designed to be a secure form of data exchange, and an NFC device is capable of being both an NFC reader and an NFC tag. This unique feature allows NFC devices to communicate peer-to-peer.

### Prerequisites
Before starting the integration you need to ready these three things:

1. Android Basics
2. NFC Enabled Device
3. NFC Tag

To enable NFC on your android device, go to Settings -> More -> and enable it

### Integration
You need to implement changes in the following parts of your Android app for basic integration of RFID Card Reader

1. Manifest 
2. Resource folder
3. Main Activity class

#### 1. Manifest
Add these lines in manifest:

-	NFC Permission
  ```
  <uses-permission android:name="android.permission.NFC" />
  ```
-	NFC Feature
  ```
  <uses-feature android:name="android.hardware.nfc" android:required="true" />
  ```
-	Intent Filter to catch action NDEF_DISCOVERED
  ```
  <intent-filter>
   <action android:name="android.nfc.action.NDEF_DISCOVERED" />
   <category android:name="android.intent.category.DEFAULT" />
   <data android:mimeType="text/plain" />
  </intent-filter>

  ```
-	Meta Data
  ```
  <meta-data
   android:name="android.nfc.action.TECH_DISCOVERED"
   android:resource="@xml/nfc_tech_filter" />

  ```

#### 2. Resource folder
Create file named **nfc_tech_filter.xml** in *-XML*- folder with the following contents:

```
<?xml version="1.0" encoding="utf-8"?>
<resources xmlns:xliff="urn:oasis:names:tc:xliff:document:1.2">
    <tech-list>
        <tech>android.nfc.tech.Ndef</tech>
        <!-- class name -->
    </tech-list>
   </resources>

```
#### 3. Main Activity class
Add these two changes & Enjoy running code with RFID NFC Card Reader Integrated
-	In OnCreate() method
  ```
  nfcAdapter = NfcAdapter.getDefaultAdapter(this);
  if (nfcAdapter == null) {
    // Stop here, we definitely need NFC
    Toast.makeText(this, "This device doesn't support NFC.", Toast.LENGTH_LONG).show();
    finish();
  }
  readFromIntent(getIntent());

  pendingIntent = PendingIntent.getActivity(this, 0, new Intent(this, getClass()).addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP), 0);
  IntentFilter tagDetected = new IntentFilter(NfcAdapter.ACTION_TAG_DISCOVERED);
  tagDetected.addCategory(Intent.CATEGORY_DEFAULT);

  ```
-	readFromIntent() method to Read from NFC Tag

  ```
  private void readFromIntent(Intent intent) {
    String action = intent.getAction();
    if (NfcAdapter.ACTION_TAG_DISCOVERED.equals(action)
            || NfcAdapter.ACTION_TECH_DISCOVERED.equals(action)
            || NfcAdapter.ACTION_NDEF_DISCOVERED.equals(action)) {
        Parcelable[] rawMsgs = intent.getParcelableArrayExtra(NfcAdapter.EXTRA_NDEF_MESSAGES);
        NdefMessage[] msgs = null;
        if (rawMsgs != null) {
            msgs = new NdefMessage[rawMsgs.length];
            for (int i = 0; i < rawMsgs.length; i++) {
                msgs[i] = (NdefMessage) rawMsgs[i];
            }
        }
        buildTagViews(msgs);
     }
 }

 ```

