DISTRIBUTIONS = jessie stretch sid
VMDEBOOTSTRAP_OPTIONS = --customize=$(CURDIR)/../helpers/vagrant-setup --package=rsync
MINOR_RELEASE=0

all:
	@echo "Usage: make <distribution>"
	@echo "Available distributions: wheezy $(DISTRIBUTIONS)"

$(DISTRIBUTIONS): % : %.box

wheezy:
	$(MAKE) wheezy.box EXTRA_VMDEBOOTSTRAP_OPTIONS='--roottype ext3'


%.box: %/box.img
	cp Vagrantfile.in $*/Vagrantfile
	cp metadata.json.in $*/metadata.json
	cd $* && tar cvfz ../$@ ./box.img ./Vagrantfile ./metadata.json

%/box.img: %.qcow2
	mkdir $*
	ln $< $@

test_all = test-wheezy $(patsubst %, test-%, $(DISTRIBUTIONS))
test: $(test_all)
$(test_all): test-% : %.box
	../helpers/vagrant-test libvirt $* $<


clean::
	rm -rf wheezy $(DISTRIBUTIONS) *.box *.tested

upload-%: test-%
		# extract debian version from inside the disk image
		$(eval RELEASE != $(CURDIR)/../helpers/get_numeric_version.pl $*.qcow2)
		$(CURDIR)/../helpers/atlas-cli.pl \
			--box $*.box \
			--version $(RELEASE).$(MINOR_RELEASE) \
			--provider libvirt \
			--changelog "* stable point release \n"

include ../vmdebootstrap-generic-qcow2/common.mk
