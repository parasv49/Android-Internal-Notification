# Android-Internal-Notification
Android Internal Notification

* Local notification can be used to fire internal notification from the application.
* They dont not require the internet connection to fire notification.
* This local notification is fired after every 1 hour.
* It can use in remainder type aaplication.



# LocalNotification.java

    public class LocalNotification extends IntentService 
    {
    public LocalNotification() {
        super("LocalNotification");
    }

    @Override
    protected void onHandleIntent(Intent intent) {
       
                send_local_notification();   //local notification trigger
    }

    private void send_local_notification() {

        Intent notificationIntent =new Intent(this, LocalActivity.class);
        notificationIntent.putExtra("local_service","100");

        TaskStackBuilder stackBuilder = TaskStackBuilder.create(this);
        stackBuilder.addParentStack(LocalActivity.class);
        stackBuilder.addNextIntent(notificationIntent);

        PendingIntent pendingIntent = stackBuilder.getPendingIntent(0, PendingIntent.FLAG_UPDATE_CURRENT);
        Uri defaultSoundUri= RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);

        Bitmap icon = BitmapFactory.decodeResource(this.getResources(),R.mipmap.ic_launcher);

        Notification notification = null;

        Intent addIntent = new Intent(this, Add.class);
        PendingIntent pendingIntentCancel = PendingIntent.getBroadcast(this, 0, addIntent, PendingIntent.FLAG_UPDATE_CURRENT);

        Intent cancelIntent=new Intent(this,CancelNotification.class);
        PendingIntent pendingCancelIntent =
                PendingIntent.getBroadcast(this, 0, cancelIntent, PendingIntent.FLAG_UPDATE_CURRENT);

        NotificationCompat.Builder notificationBuilder = new NotificationCompat.Builder(this)
                .setAutoCancel(true)
                .setContentText("Notification")
                .setContentTitle("Internal_Notification")
                .setSmallIcon(getNotificationIcon())
                .setLargeIcon(icon)
                .setSound(defaultSoundUri)
                .addAction(R.mipmap.add,"Local Notification",pendingIntentCancel)
                .addAction(R.mipmap.cancel,"Cancel",pendingCancelIntent)
                .setContentIntent(pendingIntent);

        notification = notificationBuilder.build();
        NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
        notificationManager.notify("Local",101, notification);
    }

    public int getNotificationIcon() {
        boolean useWhiteIcon = (Build.VERSION.SDK_INT>= Build.VERSION_CODES.LOLLIPOP);
        return useWhiteIcon ? R.mipmap.image: R.mipmap.image;
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        Log.d("local_service","ondestroy");
    }
    }


# CancelNotification.java

    public class CancelNotification extends BroadcastReceiver {

    private int id;

    @Override
    public void onReceive(Context context, Intent intent) {
        id = 101;
        NotificationManager notificationManager =
                (NotificationManager) context.getSystemService(Context.NOTIFICATION_SERVICE);
        notificationManager.cancel("Local",id);
    }
    }


# Add.java
    public class Add extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        Toast.makeText(context,"Add",Toast.LENGTH_LONG).show();
        NotificationManager notificationManager = (NotificationManager) context.getSystemService(Context.NOTIFICATION_SERVICE);
        notificationManager.cancel("Local", 101);
    }
    }


 
# ***Start service***
   
        AlarmManager alarmManager;
         PendingIntent pendingIntent;
     
        alarmManager = (AlarmManager) this.getSystemService(this.ALARM_SERVICE);
        long when = System.currentTimeMillis();         // notification time
        Intent intent = new Intent(this, LocalNotification.class);
        pendingIntent = PendingIntent.getService(this, 0, intent, 0);
        alarmManager.setRepeating(AlarmManager.RTC_WAKEUP, when + 10000,3600000, pendingIntent);  
        
        // when+10000 is the starting time of the service
        // 3600000 is the interval between two local notiifcation
        
        
# ***Stop Service***
      
     AlarmManager alarmManager;
     PendingIntent pendingIntent;
     
      alarmManager.cancel(pendingIntent);
        
* #service must be start and stop from the Application class.

