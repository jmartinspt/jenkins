import hudson.*
import hudson.security.*
import java.util.*
import java.lang.reflect.*
import jenkins.model.Jenkins
import org.apache.commons.lang.RandomStringUtils
import hudson.tasks.Mailer


node() {

def g_userList
def g_file_users_mail_list
def g_jk_users_list
def g_jk_delete_users_list
def g_repo_url
def g_repo_branch
def g_users_file
def g_admin_user_id

//try {
        stagePreparation()
        stageCreateUsers()
        stageDeleteUsers()
        //stageSendEmail()
 //   }
//    catch(Exception e) {
//        println "[UNSPECTED ERROR]: " + e.message
//        error e
//    }

}

def stagePreparation(){
    stage('Preparation') {
        
        // Clean Workspace
        deleteDir()
        
        // Set and initialise global vars
        g_repo_url = 'https://github.com/jmartinspt/jenkins.git'
        g_repo_branch = '*/master'
        g_users_file = 'users.json'
        g_file_users_mail_list = []
        g_jk_users_list        = []
        g_jk_delete_users_list = []
        g_admin_user_id = 'admin'

        checkout([$class: 'GitSCM', branches: [[name: g_repo_branch]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'JOEL-GITHUB', url: g_repo_url]]])
    
        g_userList = readJSON file: g_users_file, text: ''
        
        // Verify if list is not empty
        assert !g_userList.empty
        
        g_userList.each {
            g_file_users_mail_list.add(it.mail)  
        }
        
        def users = hudson.model.User.getAll()
        
        users.each {
            println("Jenkins User ID -> " + it.getId())
            
            def email_address = null
            def emailProperty = it.getProperty(hudson.tasks.Mailer.UserProperty)
            
            if(emailProperty != null) {
                email_address = emailProperty.getAddress()
                
                if  (it.getId() != g_admin_user_id) {
                
                    def user_obj  = [id:it.getId(), mail:email_address]
                    
                    g_jk_users_list << user_obj    
                    
                    if (!(g_file_users_mail_list.contains(email_address))) {
                        g_jk_delete_users_list.add(it.getId())
                        echo it.getId() + ' - ' + it.getFullName() +" will be deleted!!!"
                    }
                }
            }
        }
    }
}


def stageCreateUsers(){
    stage('Create Users') {

        g_userList.each {
            
            def user_id        = null
            def user_full_name = it.name
            def user_pass      = "123"
            def user_mail      = it.mail
            
            def find_user = g_jk_users_list.find { it.mail == user_mail }
            
            if ( find_user == null) {
                
                user_id        = getUserFromMail(user_mail)
                createUser(user_id, user_full_name, user_pass, user_mail)  
    
                println 'User ' + user_full_name + ' (' +user_id+ ') ' +  'created';
                
                // Update jenkins users list
                g_jk_users_list << [id:user_id, mail:user_mail]
            }
            else {
                println 'The email '+ user_mail +' is already used by other user ('+ find_user.id + ')!';
            }
        }
     
    }   
}

def stageDeleteUsers(){
    stage('Delete Users') {

        g_jk_delete_users_list.each {
            delete_user_by_id(it)    
        }
      
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
def createUser(String p_id, String p_full_name, String p_password, String p_mail){

        def user = hudson.model.User.get(p_id)
        user.setFullName(p_full_name)
        
        def email_param = new hudson.tasks.Mailer.UserProperty(p_mail)
        user.addProperty(email_param)
        
        def pw_param = hudson.security.HudsonPrivateSecurityRealm.Details.fromPlainPassword(p_password)
        user.addProperty(pw_param)
        
        user.save()
    
}

def delete_user_by_id(String p_id) {
    
    def user         = hudson.model.User.get(p_id, false)
    
    if (user != null) {
      user.delete()
      
      println 'User with id ' + p_id +  ' deleted';
    }
  }

def getUserFromMail(String v_email) {
   
   String candidate_user_id = (v_email.split("@")[0]).toLowerCase()
   String user_id = candidate_user_id
   int aux_value = 0
   

   while ( g_jk_users_list.find { it.id == user_id } != null) {
      
      aux_value++
      user_id = candidate_user_id + "_" + aux_value
      println('Try id - ' + user_id)
      
   } 

return user_id;

}


def getRandomPass() {

int randomStringLength = 12
String charset = (('a'..'z') + ('A'..'Z') + ('0'..'9')).join()
String randomString = RandomStringUtils.random(randomStringLength, charset.toCharArray())

return randomString

}



