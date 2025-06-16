Package com.app.catsmoker;

import android.annotation.SuppressLint;
import android.os.Build;
import android.util.Log;

import java.lang.reflect.Field;
import java.util.HashMap;
import java.util.Map;

import de.robv.android.xposed.IXposedHookLoadPackage;
import de.robv.android.xposed.XposedBridge;
import de.robv.android.xposed.callbacks.XC_LoadPackage;

@SuppressLint("DiscouragedPrivateApi")
@SuppressWarnings("ConstantConditions")
public class GameUnlocker implements IXposedHookLoadPackage {

    private static final String TAG = GameUnlocker.class.getSimpleName();

    // Map of packages to spoof with OnePlus 13 properties
    private static final Map<String, Map<String, String>> packagesToSpoof = new HashMap<String, Map<String, String>>() {{
        put("com.activision.callofduty.shooter", createOP13Props());
        put("com.activision.callofduty.warzone", createOP13Props());
        put("com.garena.game.codm", createOP13Props());
        put("com.tencent.tmgp.kr.codm", createOP13Props());
        put("com.vng.codmvn", createOP13Props());
        put("com.tencent.tmgp.cod", createOP13Props());
        put("com.tencent.ig", createOP13Props());
        put("com.pubg.imobile", createOP13Props());
        put("com.pubg.krmobile", createOP13Props());
        put("com.rekoo.pubgm", createOP13Props());
        put("com.vng.pubgmobile", createOP13Props());
        put("com.tencent.tmgp.pubgmhd", createOP13Props());
        put("com.dts.freefiremax", createOP13Props());
        put("com.dts.freefireth", createOP13Props());
        put("com.epicgames.fortnite", createOP13Props());
    }};

    @Override
    public void handleLoadPackage(XC_LoadPackage.LoadPackageParam loadPackageParam) {
        String packageName = loadPackageParam.packageName;

        if (packagesToSpoof.containsKey(packageName)) {
            Map<String, String> propsToChange = packagesToSpoof.get(packageName);
            if (propsToChange != null) {
                spoofProperties(propsToChange);
                XposedBridge.log("Spoofed " + packageName + " as OnePlus 13");
            }
        }
    }

    private static void spoofProperties(Map<String, String> properties) {
        for (Map.Entry<String, String> entry : properties.entrySet()) {
            setPropValue(entry.getKey(), entry.getValue());
        }
    }

    private static void setPropValue(String key, String value) {
        try {
            Log.d(TAG, "Setting property " + key + " to " + value);
            Field field = Build.class.getDeclaredField(key);
            field.setAccessible(true);
            field.set(null, value);
            field.setAccessible(false);
        } catch (NoSuchFieldException | IllegalAccessException e) {
            String errorMessage = "Failed to set property: " + key + " to " + value;
            Log.e(TAG, errorMessage, e);
            XposedBridge.log(errorMessage + "\n" + Log.getStackTraceString(e));
        }
    }

    /**
     * Creates a map of properties to spoof a device as a OnePlus 13.
     * Note: The model name "PJE110" is a placeholder for OnePlus 13's potential model number.
     * You might need to update this if the official model number for OnePlus 13 is different.
     */
    private static Map<String, String> createOP13Props() {
        Map<String, String> props = new HashMap<>();
        props.put("MANUFACTURER", "OnePlus");
        // Placeholder model number for OnePlus 13. Please verify and update if needed.
        props.put("MODEL", "PJE110");
        return props;
    }
}
