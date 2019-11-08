import hudson.*
import hudson.security.*
import java.util.*
import com.michelin.cio.hudson.plugins.rolestrategy.*
import java.lang.reflect.*
import jenkins.model.Jenkins
import org.apache.commons.lang.RandomStringUtils
import hudson.tasks.Mailer

node() {


String g_userId
String g_userPass
def    g_userInput
def    g_globalRolesArr
def    g_projectRolesArr

//stagePreparation()
//stageInput()
stageInput2()
stageCreateUser()
//stageAssignRoles()
stageSaveChanges()
//stageSendEmail()

}

def stagePreparation(){
    stage('Preparation') {
 
     g_globalRolesArr = []
     g_projectRolesArr = []
     def authStrategy = jenkins.model.Jenkins.instance.getAuthorizationStrategy()
     RoleBasedAuthorizationStrategy roleAuthStrategy = (RoleBasedAuthorizationStrategy) authStrategy
     RoleMap globalRoles = roleAuthStrategy.getRoleMap(RoleBasedAuthorizationStrategy.GLOBAL);
     RoleMap projectRoles = roleAuthStrategy.getRoleMap(RoleBasedAuthorizationStrategy.PROJECT);


        echo '*Global Roles*'
        globalRoles.getRoles().each{gr ->
           g_globalRolesArr << gr.getName()
        }
        
        echo '*Project Roles*'
        projectRoles.getRoles().each{pr ->
           g_projectRolesArr << pr.getName()
        }    
    
    }
}


def stageInput(){
    stage('Input') {
        
    g_userInput = input id: 'UserInput', message: 'Parâmetros:', parameters: [string(defaultValue: '', description: 'Email do colaborador', name: 'I_EMAIL', trim: false), choice(choices: g_globalRolesArr, description: 'Perfil do Colaborador', name: 'I_PERFIL'), choice(choices: g_projectRolesArr, description: 'Equipa do colaborador', name: 'I_EQUIPA')]          
        
    }   
}

def stageInput2(){
    stage('Input2') {
        
    g_userInput = input id: 'UserInput', message: 'Parameters:', parameters: [string(defaultValue: '', description: 'Email do colaborador', name: 'I_EMAIL', trim: false), string(defaultValue: '', description: 'Equipa do colaborador', name: 'I_TEAM', trim: false)]          
        
    }   
}


def stageCreateUser(){
    stage('Create User') {

    g_userId = getUserFromMail(g_userInput.I_EMAIL)
    g_userPass = getRandomPass()      
        
    createUser(g_userId ,g_userPass, g_userInput.I_EMAIL)
    
    println 'userPass'
    println g_userPass

    }   
}

def stageAssignRoles(){
    stage('Assign Roles') {
        
    assignGlobalRole(g_userId,g_userInput.I_PERFIL)
    assignProjectRole(g_userId,g_userInput.I_EQUIPA)      
        
    }   
}

def stageSaveChanges(){
    stage('Save changes') {
        
    jenkins.model.Jenkins.instance.save()     
        
    }   
}

def stageSendEmail(){
    
    stage('Send Email'){
        
    emailext bcc: '', 
    mimeType: 'text/html', 
    cc: '', 
    from: '', 
    replyTo: '', 
    subject: 'Credenciais de acesso ao Jenkins', 
    to: g_userInput.I_EMAIL,
    body: '''<!doctype html>
          <html>
            <head>
              <meta name="viewport" content="width=device-width" />
              <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
              <title>Acesso Ao Jenkins</title>
              <style>
                /* -------------------------------------
                    GLOBAL RESETS
                ------------------------------------- */

                /*All the styling goes here*/

                body {
                  background-color: #f6f6f6;
                  font-family: sans-serif;
                  -webkit-font-smoothing: antialiased;
                  font-size: 14px;
                  line-height: 1.4;
                  margin: 0;
                  padding: 0;
                  -ms-text-size-adjust: 100%;
                  -webkit-text-size-adjust: 100%;
                }
                table {
                  border-collapse: separate;
                  mso-table-lspace: 0pt;
                  mso-table-rspace: 0pt;
                  width: 100%; }
                  table td {
                    font-family: sans-serif;
                    font-size: 14px;
                    vertical-align: top;
                }
                /* -------------------------------------
                    BODY & CONTAINER
                ------------------------------------- */
                .body {
                  background-color: #f6f6f6;
                  width: 100%;
                }
                /* Set a max-width, and make it display as block so it will automatically stretch to that width, but will also shrink down on a phone or something */
                .container {
                  display: block;
                  margin: 0 auto !important;
                  /* makes it centered */
                  max-width: 800px;
                  padding: 10px;
                  width: 800px;
                }
                /* This should also be a block element, so that it will fill 100% of the .container */
                .content {
                  box-sizing: border-box;
                  display: block;
                  margin: 0 auto;
                  max-width: 800px;
                  padding: 10px;
                }
                /* -------------------------------------
                    HEADER, FOOTER, MAIN
                ------------------------------------- */
                .main {
                  background: #ffffff;
                  border-radius: 3px;
                  width: 100%;
                }
                .wrapper {
                  box-sizing: border-box;
                  padding: 20px;
                }
                .content-block {
                  padding-bottom: 10px;
                  padding-top: 10px;
                }
                .footer {
                  clear: both;
                  margin-top: 75px;
                  text-align: center;
                  width: 100%;
                }
                  .footer td,
                  .footer p,
                  .footer span,
                  .footer a {
                    color: #999999;
                    font-size: 12px;
                    text-align: center;
                }
                /* -------------------------------------
                    TYPOGRAPHY
                ------------------------------------- */
                h1,
                h2,
                h3,
                h4 {
                  color: #000000;
                  font-family: sans-serif;
                  font-weight: 400;
                  line-height: 1.4;
                  margin: 0;
                  margin-bottom: 30px;
                }
                h1 {
                  font-size: 35px;
                  font-weight: 300;
                  text-align: center;
                  text-transform: capitalize;
                }
                p,
                ul,
                ol {
                  font-family: sans-serif;
                  font-size: 14px;
                  font-weight: normal;
                  margin: 0;
                  margin-bottom: 15px;
                }
                  p li,
                  ul li,
                  ol li {
                    list-style-position: inside;
                    margin-left: 5px;
                }
                a {
                  color: #3498db;
                  text-decoration: underline;
                }




              </style>
            </head>
            <body class="">
              <table role="presentation" border="0" cellpadding="0" cellspacing="0" class="body">
                <tr>
                  <td>&nbsp;</td>
                  <td class="container">
                    <div class="content">

                      <table role="presentation" class="main">

                        <!-- START MAIN CONTENT AREA -->
                        <tr>
                          <td class="wrapper">
                            <table role="presentation" border="0" cellpadding="0" cellspacing="0">
                              <tr>
                                <td>
                                  <p>Olá '''+g_userId+''',</p>
                                  <p>Foram criadas as tuas credenciais para o acesso ao <a href="http://jenkins.spms.min-saude.pt" target="_blank">Jenkins</a> CI/CD da SPMS.</p>
                                   <br/>
          						 <br/>
          						<p><b>UserName: </b>'''+g_userId+'''</p>
          						<p><b>Password: </b>'''+g_userPass+'''</p>
          						<p><b>Perfil: </b>'''+g_userInput.I_PERFIL+'''</p>
          						<p><b>Equipa: </b>'''+g_userInput.I_EQUIPA+'''</p>          						
          						<br/>
								<br/>
								<br/>
								<p>Poderás alterar as tuas informações pessoais e a tua password nas <a href="http://jenkins.spms.min-saude.pt/user/'''+g_userId+'''/configure" target="_blank">configurações</a> do teu perfil.</p> 
                                </td>
          					  <br/>
          					  <br/>
                              </tr>
                            </table>
                          </td>
                        </tr>

                      <!-- END MAIN CONTENT AREA -->
                      </table>

                      <!-- START FOOTER -->
                      <div class="footer">
                        <table role="presentation" border="0" cellpadding="0" cellspacing="0">
                          <tr>
                            <td class="content-block">
                              <span class="apple-link">Engenharia de Software</span>
                            </td>
                          </tr>
						  <tr>
                            <td class="content-block">
                              <span class="apple-link">Direção de Sistemas de Informação</span>
                            </td>
                          </tr>
						  <tr>
                            <td class="content-block">
                              <span class="apple-link">SERVIÇOS PARTILHADOS DO MINISTÉRIO DA SAÚDE, EPE</span>
                            </td>
                          </tr>
                          <tr>
                            <td class="content-block powered-by">
                              Email gerado pelo Jenkins
                            </td>
                          </tr>
                        </table>
                      </div>
                      <!-- END FOOTER -->

                    <!-- END CENTERED WHITE CONTAINER -->
                    </div>
                  </td>
                  <td>&nbsp;</td>
                </tr>
              </table>
            </body>
          </html>'''    
        
    }
    
}

//Criacao do usuário
def createUser(pUser, pPass, pMail){

    try {
       // jenkins.model.Jenkins.instance.securityRealm.createAccount(pUser, pPass);
        def instance = jenkins.model.Jenkins.instance
        def user = instance.securityRealm.createAccount(pUser, pPass)
        user.addProperty(new Mailer.UserProperty(pMail));
        println "Utilizador '" + pUser + "' criado com sucesso."   ;
    }
    catch(Exception e) {
        println "[ERRO]: " + e.message
    }
}


def assignProjectRole(pUser, pProjectRole){
	
	def authStrategy = jenkins.model.Jenkins.instance.getAuthorizationStrategy()

	
	//criacao de role e atribuicao
	if(authStrategy instanceof RoleBasedAuthorizationStrategy){
		RoleBasedAuthorizationStrategy roleAuthStrategy = (RoleBasedAuthorizationStrategy) authStrategy

		RoleMap roles = roleAuthStrategy.getRoleMap(RoleBasedAuthorizationStrategy.PROJECT);

		Role targetRole = roles.getRole(pProjectRole);

		if (targetRole != null) {
			roleAuthStrategy.assignRole(RoleBasedAuthorizationStrategy.PROJECT, targetRole, pUser);
			jenkins.model.Jenkins.instance.setAuthorizationStrategy(roleAuthStrategy)
			println "Regra '"+pProjectRole+"' associada ao usuario ."
		}
		else{
			println "ERRO: Regra '"+pProjectRole+"' nao encontrada."
		}	  

		println "OK"
	}
	else {
		println "Role Strategy Plugin nao encontrado!"
	}
}

def assignGlobalRole(pUser, pProjectRole){
	
	def authStrategy =  jenkins.model.Jenkins.instance.getAuthorizationStrategy()

	
	//criacao de role e atribuicao
	if(authStrategy instanceof RoleBasedAuthorizationStrategy){
		RoleBasedAuthorizationStrategy roleAuthStrategy = (RoleBasedAuthorizationStrategy) authStrategy

		RoleMap roles = roleAuthStrategy.getRoleMap(RoleBasedAuthorizationStrategy.GLOBAL);

		Role targetRole = roles.getRole(pProjectRole);

		if (targetRole != null) {
			roleAuthStrategy.assignRole(RoleBasedAuthorizationStrategy.GLOBAL, targetRole, pUser);
			
			jenkins.model.Jenkins.instance.setAuthorizationStrategy(roleAuthStrategy)
			
			println "Regra '"+pProjectRole+"' associada ao usuario ."
		}
		else{
			println "ERRO: Regra '"+pProjectRole+"' nao encontrada."
		}	  

		println "OK"
	}
	else {
		println "Role Strategy Plugin nao encontrado!"
	}
}


def getUserFromMail(v_email) {
   
   String userId = v_email.split("@")[0];


return userId.toLowerCase();

}


def getRandomPass() {

int randomStringLength = 12
String charset = (('a'..'z') + ('A'..'Z') + ('0'..'9')).join()
String randomString = RandomStringUtils.random(randomStringLength, charset.toCharArray())

return randomString

}


/*
Sugestão de criação de regras
========================================
fonte: https://documentation.cloudbees.com/docs/cje-user-guide/rbac-sect-sample-configs.html
*/


