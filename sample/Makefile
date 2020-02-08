# This Makefile helps demonstrate how python_secrets can be used for
# managing insertion of secrets into a cloud-config file maintained
# outside of the source code repository. This minimizes the possibility
# of accidental commit+push of a file including secrets. It also has
# rules enabling downloading an validation of the HypriotOS image
# to be used.  For more information, see:
#
#   https://github.com/davedittrich/python_secrets

SHELL=/bin/bash
TMP:=$(shell psec environments path --tmpdir)
HYPRIOT_VERSION=1.12.0
HYPRIOT_IMG=hypriotos-rpi-v$(HYPRIOT_VERSION).img
HYPRIOT_ZIP=$(HYPRIOT_IMG).zip
HYPRIOT_ZIP_URL=https://github.com/hypriot/image-builder-rpi/releases/download/v$(HYPRIOT_VERSION)/$(HYPRIOT_ZIP)
HYPRIOT_SHA256=$(HYPRIOT_ZIP).sha256
HYPRIOT_SHA256_URL=$(HYPRIOT_ZIP_URL).sha256
CLOUD_CONFIG_SRC:=wifi-device-init-secure.j2
CLOUD_CONFIG:=$(TMP)/cloud-config
IMAGE=$(HYPRIOT_IMG)

.PHONY: all
all: help

.PHONY: help
help:
	@echo 'usage: make target [...]'
	@echo ''
	@echo 'TARGETS:'
	@echo 'cloud-config - generate cloud-config file from template "cloud-config.j2"'
	@echo 'sd - create SD card'
	@echo 'clean - delete temporary files'
	@echo 'hypriot-img - download $(HYPRIOT_ZIP) file, check SHA256 sum, unzip'
	@echo ''
	@echo 'DEVELOPMENT/DEBUGGING TARGETS:'
	@echo 'variables - show variables'
	@echo ''
	@echo 'To generate new secrets, run "psec secrets generate"'
	@echo 'To set undefined string secrets, run "psec secrets set --undefined"'

.PHONY: cloud-config
cloud-config: $(CLOUD_CONFIG)

$(CLOUD_CONFIG): $(CLOUD_CONFIG_SRC)
	@[ -d $(TMP) ] || (echo "TMP does not point to a valid directory" && exit 1)
	psec -E template $< $@

.PHONY: hypriot-zip
hypriot-zip $(HYPRIOT_ZIP):
	wget -nc $(HYPRIOT_ZIP_URL)
	wget -nc $(HYPRIOT_SHA256_URL)
	sha256sum -c $(HYPRIOT_SHA256)

.PHONY: hypriot-img
hypriot-img $(HYPRIOT_IMG): $(HYPRIOT_ZIP)
	@[ ! -f $(HYPRIOT_IMG) ] && unzip $(HYPRIOT_ZIP) || ls -l $(HYPRIOT_IMG)

.PHONY: flash
flash:
	@[ -f $(IMAGE) ] || (echo "[-] set IMAGE with path to OS image" && exit 1)
	flash --userdata $(CLOUD_CONFIG) $(IMAGE)


.PHONY: clean
clean:
	[ ! -f "$(CLOUD_CONFIG)" ] || rm "$(CLOUD_CONFIG)"
	[ ! -f "$(HYPRIOT_IMG)" ] || rm "$(HYPRIOT_IMG)"

.PHONY: clean
spotless: clean
	[ ! -f "$(HYPRIOT_ZIP)" ] || rm "$(HYPRIOT_ZIP)"
	[ ! -f "$(HYPRIOT_SHA256)" ] || rm "$(HYPRIOT_SHA256)"

.PHONY: variables
variables: Makefile
	@echo TMP="$(TMP)"
	@echo CLOUD_CONFIG="$(CLOUD_CONFIG)"
	@echo \$\@=$@
	@echo \$\<=$<


.PHONY: debug-server
debug-server:
	tar -cf - gosecure_app.py forms.py templates/ scripts/ | ssh gosecure sudo tar -C goSecure_Web_GUI/ -xvf -
	ssh gosecure sudo service gosecure restart
