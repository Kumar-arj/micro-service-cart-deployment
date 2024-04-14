  def label = "shopagent"
  def env = "stage"
  podTemplate(label: label, yaml: """
  apiVersion: v1
  kind: Pod
  metadata:
    labels:
      name: build
  spec:
    serviceAccount: jenkins-admin
    containers:
    - name: build
      image: kumararj/eos-jenkins-agent-base:latest
      command:
      - cat
      tty: true
  """
  ) {
      node (label) {

          stage ('Checkout SCM'){
            git credentialsId: 'git', url: 'https://github.com/Kumar-arj/micro-service-cart-deployment.git', branch:  "${env}"
          }

          stage ('Helm Chart') {
            container('build') {
                withCredentials([usernamePassword(credentialsId: 'nexuslogin', usernameVariable: 'username', passwordVariable: 'password')]) {
                      sh '/usr/local/bin/helm repo add sock-shop-helm-local http://nexus.k4m.in/repository/sock-shop-helm-local/ --username $username --password $password'
                      sh "/usr/local/bin/helm repo update"
                      sh "/usr/local/bin/helm upgrade  --install --force micro-services-cart  --namespace ${env} -f values.yaml sock-shop-helm-local/micro-services-cart"
                      sh "/usr/local/bin/helm list -a --namespace ${env}"
                      sh "rm -rf values.yaml"
                      
              }
          }
          }
      }
  }