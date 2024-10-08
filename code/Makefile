SHELL := bash

all: requirements.txt

# Variables for project configuration
REQUIREMENTS := requirements.txt
CONDA_ENV := spark
PYTHON_VERSION := 3.11
PACKAGES := pyspark pyarrow requests ipython jupyterlab pandas lxml pre-commit ruff black pickleshare openpyxl

# Java, Spark, and Hadoop variables
JAVA_VERSION := 11
SPARK_VERSION := 3.5.2
HADOOP_VERSION := 3.3.6

# Recipes
$(REQUIREMENTS):
	$(MAKE) setup

.PHONY: setup activate clean remake run setup-pre-commit run-pre-commit install-java install-spark install-hadoop

# Install Java
install-java:
	@echo "Installing Java $(JAVA_VERSION)..."
	brew install openjdk@$(JAVA_VERSION)
	@echo "export JAVA_HOME=\"$$(/usr/libexec/java_home -v $(JAVA_VERSION))\"" >> ~/.bashrc
	@echo "Java $(JAVA_VERSION) installed. Please restart your terminal or run 'source ~/.bashrc' to update your environment."

# Install Apache Spark
install-spark:
	@echo "Installing Apache Spark $(SPARK_VERSION)..."
	brew install apache-spark
	@echo "Apache Spark $(SPARK_VERSION) installed."

# Install Hadoop
install-hadoop:
	@echo "Installing Hadoop $(HADOOP_VERSION)..."
	brew install hadoop
	@echo "export HADOOP_HOME=\"/usr/local/opt/hadoop/libexec\"" >> ~/.bashrc
	@echo "export PATH=\"$$HADOOP_HOME/bin:$$PATH\"" >> ~/.bashrc
	@echo "export LD_LIBRARY_PATH=\"$$HADOOP_HOME/lib/native:$$LD_LIBRARY_PATH\"" >> ~/.bashrc
	@echo "Hadoop $(HADOOP_VERSION) installed. Please restart your terminal or run 'source ~/.bashrc' to update your environment."

# Create the conda environment and install the required packages
setup: install-java install-spark install-hadoop
	conda init bash
	conda create -n $(CONDA_ENV) python=$(PYTHON_VERSION) -y
	$(MAKE) activate
	source $$(conda info --base)/etc/profile.d/conda.sh && conda activate $(CONDA_ENV)
	$(MAKE) install-packages
	@echo "Now, you activate the conda environment with 'conda activate $(CONDA_ENV)'"

install-packages:
	@echo "Activating conda environment '$(CONDA_ENV)' and installing packages..."
	. $$(conda info --base)/etc/profile.d/conda.sh && \
	conda activate $(CONDA_ENV) && \
	pip install $(PACKAGES) && \
	pip freeze > $(REQUIREMENTS)
	@echo "Packages installed. Requirements exported to $(REQUIREMENTS)"

# Remake the conda environment
remake: clean
	@echo "Recreating conda environment '$(CONDA_ENV)' with Python $(PYTHON_VERSION)..."
	$(MAKE) setup

clean:
	@echo "Deleting conda environment '$(CONDA_ENV)'..."
	conda env remove -n $(CONDA_ENV) -y

setup-pre-commit:
	@echo "Setting up pre-commit hooks..."
	. $$(conda info --base)/etc/profile.d/conda.sh && \
	conda activate $(CONDA_ENV) && \
	pre-commit install

run-pre-commit:
	@echo "Running pre-commit hooks on all files..."
	. $$(conda info --base)/etc/profile.d/conda.sh && \
	conda activate $(CONDA_ENV) && \
	pre-commit run --all-files

# Test code
run:
	@echo "Running main.py with the correct Python interpreter..."
	. $$(conda info --base)/etc/profile.d/conda.sh && \
	conda activate $(CONDA_ENV) && \
	python -m main
