from azureml.core import Workspace, Model, Environment
from azureml.core.model import InferenceConfig
from azureml.core.webservice import AciWebservice

# Conectar-se ao seu workspace do Azure ML
ws = Workspace.get(name='nome-do-workspace', subscription_id='sua-subscription-id', resource_group='nome-do-resource-group')

# Carregar o modelo treinado
model = Model(ws, 'nome-do-modelo')

# Definir configuração de inferência
inference_config = InferenceConfig(entry_script='score.py', environment=Environment.from_conda_specification(name='myenv', file_path='conda_dependencies.yml'))

# Configurar o serviço web
deployment_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

# Implantação do serviço web
service = Model.deploy(workspace=ws,
                       name='nome-do-servico',
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=deployment_config)

service.wait_for_deployment(show_output=True)

# Obter o endpoint do serviço
endpoint = service.scoring_uri
print(endpoint)
