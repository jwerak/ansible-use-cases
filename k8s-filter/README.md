# k8s filtering

The motivation behind this example is discussion with a customer, who wanted to filter out and summarize k8s pods with GPU enabled.

We've talked about several options including creating mutating webhook to set label on Pods and to allow easy filtering, or using cli tool to filter k8s output, such as go template in kubectl, or jq cli tool.

Even though I love to have a quick cli way to get the information, it might not be good enough for other colleagues or managers who might not have the skills, tools, or righ version of the script.
This sample is showing how this can be done using Ansible which in turn gives you possibility to generate report on schedule, or give access to generated infromation to broader audience, which might not know how to do it, or doesn't have authorization for given cluster.
