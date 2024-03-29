Content-Type: text/x-zim-wiki
Wiki-Format: zim 0.4
Creation-Date: 2019-12-18T14:55:35+09:00

====== Adding a datacenter to a cluster ======
Created 수요일 18 12월 2019

====== 절차 ======
1. 이전 데이터 파일이 새로운 클러스터를 방해하지 않는지 확인합니다.
	a. 이전 데이터가 포함된 다른 클러스터 또는 데이터센터에서 기존(실행중인) 노드를 제거합니다.
	b. 노드를 올바르게 정리합니다.
	c. 응용 프로그램 디렉토리를 완전히 제거합니다 (권장)
	d. 제거 후 Cassandra를 처음부터 설치합니다.

2. Keyspaces를 구성하고 새 datacenter를 생성합니다.
	a. 다음 keyspaces에 대해 NetworkTopologyStrategy를 사용하기 위해 ALTER KEYSPACE 를 사용합니다.
		1. 사용자가 만든 모든 항목
		2. 시스템: system_distributed, system_auth, system_traces
		이 단계는 지정된 소스 데이터 센터에 이러한 keyspaces의 복제가 nodetool rebuild에서 필요하기 때문에 multiple datacenter clusters에 필요합니다.
	b. replication factor가 0인 새로운 datacenter를 생성합니다.
		cqlsh를 사용하여 keyspace를 생성(create)하거나 변경(alter)할 수 있습니다.
{{{code: lang="sql" linenumbers="True"
CREATE KEYSPACE "sample-ks" WITH REPLICATION =
{ 'class' : 'NetworkTopologyStrategy', 'ExistingDC' : 3 , 'NewDC' : 0};
}}}
		
3. 새로운 datacenter에서, 각각의 새로운 노드에 Cassandra를 설치합니다. 서비스를 시작하거나 노드를 재시작하지 마십시오.
	클러스터의 모든 노드에서 동일한 버전의 Cassandra를 사용해야 합니다.
4. 클러스터의 다른 노드를 구성한 후 각 새 노드에 cassandra.yaml 을 구성합니다.
	a. 클러스터의 다른 노드에 있는 cassandra.yaml 파일의 설정과 일치하도록 -seeds 와 endpoint_snitch 같은 cassandra.yaml 의 값을 설정하십시오.  [[https://docs.datastax.com/en/cassandra-oss/3.x/cassandra/initialize/initMultipleDS.html|Initializing a multiple node cluster (multiple datacenters)]] 를 참조하십시오.
