# fragment_stream.xml.xml Source Code

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="ai.livestyler.LiveStylerSDKAndroid.StreamFragment">

    <FrameLayout
        android:id="@+id/render_view_container"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="@color/black"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent">

        <org.webrtc.SurfaceViewRenderer
            android:id="@+id/render_view"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_gravity="center"
            />

    </FrameLayout>

    <androidx.appcompat.widget.AppCompatImageView
        android:id="@+id/imageview_logo"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginStart="20dp"
        android:layout_marginTop="33.5dp"
        android:layout_marginEnd="20dp"
        android:src="@drawable/logo_live_styler"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        />

    <TextView
        android:id="@+id/textview_local_stream"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginStart="20dp"
        android:layout_marginTop="4dp"
        android:layout_marginEnd="20dp"
        android:layout_marginBottom="4dp"
        style="@style/TextAppearance.MaterialComponents.Caption"
        android:text="@string/provisioning_server_stream"
        android:textSize="10sp"
        android:textStyle="bold"
        android:textColor="@color/white"
        android:textAlignment="textEnd"
        app:layout_constraintTop_toBottomOf="@id/imageview_logo"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintBottom_toTopOf="@id/textview_remote_stream"
        tools:ignore="SmallSp" />

    <TextView
        android:id="@+id/textview_remote_stream"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginStart="20dp"
        android:layout_marginTop="4dp"
        android:layout_marginEnd="20dp"
        android:layout_marginBottom="16dp"
        style="@style/TextAppearance.MaterialComponents.Caption"
        android:text="@string/provisioning_server_stream"
        android:textSize="10sp"
        android:textStyle="bold"
        android:textColor="@color/white"
        android:textAlignment="textEnd"
        app:layout_constraintTop_toBottomOf="@id/textview_local_stream"
        app:layout_constraintEnd_toEndOf="parent"
        tools:ignore="SmallSp" />

    <FrameLayout
        android:id="@+id/preview_container"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="1dp"
        android:layout_marginTop="33.5dp"
        android:background="@color/black"
        app:layout_constraintTop_toBottomOf="@id/imageview_logo"
        app:layout_constraintStart_toStartOf="@id/imageview_logo">

        <org.webrtc.SurfaceViewRenderer
            android:id="@+id/preview"
            android:layout_width="90dp"
            android:layout_height="160dp"
            android:layout_gravity="center"
            />

    </FrameLayout>

    <Spinner
        android:id="@+id/spinner_camera"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        app:layout_constraintStart_toStartOf="@id/preview_container"
        app:layout_constraintTop_toBottomOf="@id/preview_container"
        app:layout_constraintEnd_toEndOf="@id/preview_container"
        android:paddingEnd="0dp"
        tools:listitem="@layout/support_simple_spinner_dropdown_item"
        tools:ignore="RtlSymmetry" />

    <include
        layout="@layout/button_style_panel"
        android:id="@+id/style_panel_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginEnd="20dp"
        android:layout_marginBottom="35dp"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintHorizontal_bias="1"
        app:layout_constraintVertical_bias="1"
        />

    <include
        layout="@layout/layout_style_panel"
        android:id="@+id/style_panel"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintVertical_bias="1"
        android:visibility="invisible"
        />

</androidx.constraintlayout.widget.ConstraintLayout>
```