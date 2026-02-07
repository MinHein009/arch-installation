# ==============================================
# ၁။ Partition ပြင်ဆင်ခြင်း
# ==============================================

# Partition ခွဲရန်
cfdisk /dev/sda

# Format ချရန်
mkfs.fat -F 32 /dev/sda1
mkswap /dev/sda2
mkfs.ext4 /dev/sda3

# Mount လုပ်ရန်
mount /dev/sda3 /mnt
mount --mkdir /dev/sda1 /mnt/boot
swapon /dev/sda2

# ==============================================
# ၂။ Base System သွင်းခြင်း
# ==============================================

# အခြေခံ Package များ သွင်းရန်
pacstrap -K /mnt base linux linux-firmware nano vi

# fstab ထုတ်ရန်
genfstab -U /mnt >> /mnt/etc/fstab

# စက်အသစ်ထဲ ကူးဝင်ရန်
arch-chroot /mnt

# ==============================================
# ၃။ System Config (စက်ထဲရောက်မှ ရိုက်ရန်)
# ==============================================

# Timezone သတ်မှတ်ရန်
ln -sf /usr/share/zoneinfo/Asia/Yangon /etc/localtime
hwclock --systohc

# Locale သတ်မှတ်ရန် (en_US.UTF-8 ကို nano နဲ့ uncomment အရင်လုပ်ပါ)
nano /etc/locale.gen
# locale.gen ဖိုင်ထဲမှာ "en_US.UTF-8 UTF-8" ဆိုတဲ့ line ကို ရှာပြီး ရှေ့က # ကို ဖြုတ်ပါ
# Ctrl+X နှိပ်၊ Y နှိပ်၊ Enter နှိပ် ပြီးထွက်ပါ
locale-gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf

# စက်နာမည်ပေးရန်
echo "archlinux" > /etc/hostname

# ==============================================
# ၄။ User နှင့် Password (အရေးကြီးဆုံး)
# ==============================================

# Root Password ပေးရန်
passwd
# password ရိုက်ထည့်ပါ (ဖော်ပြမထားဘဲ ရိုက်ရမည်၊ နှစ်ကြိမ်တူညီစွာ ရိုက်ပါ)

# User အသစ်ဆောက်ပြီး Password ပေးရန်
useradd -m -G wheel grayhoddie
passwd grayhoddie
# user password ရိုက်ထည့်ပါ (ဖော်ပြမထားဘဲ ရိုက်ရမည်၊ နှစ်ကြိမ်တူညီစွာ ရိုက်ပါ)

# Sudo ခွင့်ပြုချက်ပေးရန်
EDITOR=nano visudo
# visudo ဖိုင်ထဲမှာ "%wheel ALL=(ALL:ALL) ALL" ဆိုတဲ့ line ကို ရှာပြီး ရှေ့က # ကို ဖြုတ်ပါ
# Ctrl+X နှိပ်၊ Y နှိပ်၊ Enter နှိပ် ပြီးထွက်ပါ

# ==============================================
# ၅။ Bootloader (GRUB) နှင့် GUI
# ==============================================

# လိုအပ်သော Package များ သွင်းရန်
pacman -S grub efibootmgr dosfstools mtools plasma-desktop sddm networkmanager konsole dolphin virtualbox-guest-utils

# GRUB Install လုပ်ရန်
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB --removable
grub-mkconfig -o /boot/grub/grub.cfg

# Service များ ဖွင့်ရန်
systemctl enable sddm
systemctl enable NetworkManager
systemctl enable vboxservice

# ==============================================
# ၆။ အပြီးသတ်ထွက်ခြင်း
# ==============================================

# Arch-chroot မှ ထွက်ရန်
exit

# Mount ဖြုတ်ရန်
umount -R /mnt

# ပြန်ဖွင့်ရန်
reboot

မှတ်ချက်: ဤကုဒ်အားလုံးကို အဆင့်ဆင့်အလိုက် တစ်ခုပြီးတစ်ခု run ရပါမည်။ ပထမအပိုင်းပြီးမှ နောက်အပိုင်းကို ဆက်ရိုက်ပါ။ သတိထားရန် - passwd command ရိုက်ပြီးတိုင်း password ကို မြင်ရမည်မဟုတ်ပါ (ပုံမှန်အတိုင်း ဖြစ်ပါသည်)။
