# Enable-Disable TocuhPad and Keyboard of Laptop 

## 1. `/usr/local/bin/disablekm`

**Purpose:** Disable both the internal keyboard and touchpad.
**Mechanism:**

* Unbinds the keyboard driver (`i8042`)
* Unbinds the touchpad driver (`hid-multitouch`)

```bash
#!/bin/bash
# disable internal keyboard
echo "i8042" | sudo tee /sys/bus/platform/drivers/i8042/unbind 2>/dev/null
# disable touchpad
echo -n "0018:0488:1014.0005" | sudo tee /sys/bus/hid/drivers/hid-multitouch/unbind 2>/dev/null
echo "Internal keyboard and touchpad disabled."
```

---

## 2. `/usr/local/bin/enablekm`

**Purpose:** Re-enable both the internal keyboard and touchpad.
**Mechanism:**

* Binds the keyboard driver (`i8042`)
* Binds the touchpad driver (`hid-multitouch`)

```bash
#!/bin/bash
# enable internal keyboard
echo "i8042" | sudo tee /sys/bus/platform/drivers/i8042/bind 2>/dev/null
# enable touchpad
echo -n "0018:0488:1014.0005" | sudo tee /sys/bus/hid/drivers/hid-multitouch/bind 2>/dev/null
echo "Internal keyboard and touchpad enabled."
```

---

## 3. Usage

Make the scripts executable:

```bash
sudo chmod +x /usr/local/bin/disablekm
sudo chmod +x /usr/local/bin/enablekm
```

Run them:

```bash
sudo disablekm   # disables keyboard + touchpad
sudo enablekm    # enables keyboard + touchpad
```

---

## 4. Notes

* These scripts directly manipulate kernel drivers under `/sys/bus/...`
* Changes last until reboot (after reboot, both devices are automatically enabled again).
* Requires **sudo** since binding/unbinding drivers is a privileged action.
