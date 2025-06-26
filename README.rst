<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:background="#0a3d62"
    tools:context=".MainActivity">
    
    <!-- Result display text -->
    <TextView
        android:id="@+id/resultTextView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textSize="24sp"
        android:textColor="#FFFFFF"
        android:text="Good Luck!"
        android:layout_marginBottom="30dp"/>
    
    <!-- Slot reels container -->
    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:background="#1e3799"
        android:padding="20dp">
      
        <!-- Reel 1 (Cherry) -->
        <ImageView
            android:id="@+id/reel1"
            android:layout_width="100dp"
            android:layout_height="100dp"
            android:src="@drawable/ic_cherry"
            android:padding="10dp"/>
        
        <!-- Reel 2 (Lemon) -->
        <ImageView
            android:id="@+id/reel2"
            android:layout_width="100dp"
            android:layout_height="100dp"
            android:src="@drawable/ic_lemon"
            android:padding="10dp"/>
        
        <!-- Reel 3 (Seven) -->
        <ImageView
            android:id="@+id/reel3"
            android:layout_width="100dp"
            android:layout_height="100dp"
            android:src="@drawable/ic_seven"
            android:padding="10dp"/>
    </LinearLayout>
    
    <!-- Spin button -->
    <Button
        android:id="@+id/spinButton"
        android:layout_width="200dp"
        android:layout_height="60dp"
        android:text="SPIN!"
        android:textSize="20sp"
        android:background="#4a69bd"
        android:textColor="#FFFFFF"
        android:layout_marginTop="40dp"/>
</LinearLayout>