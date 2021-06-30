# Ansible

Answers and/or workarounds to questions encountered when using Ansible.

## Rationale

[Ansible](https://www.ansible.com/) is a great tool which allows for effective
bootstrap and lifecycle automation.  

The purpose of this repo is to help quickly find answers or more likely
workarounds (if not hacks) in case of bumping into the same or similar
questions.  

>> These code snippets are not solutions and although it may help to solve some
>> problem, it may also bring in some other issues. Use at your own risk.  

## Ansible how to gather value from one host and use it on other hosts?

What if we want to find out the value of the variable on one particular host and
set this value on other hosts?  

First, gather the value of the variable (e.g. erlang cookie) from desired host:  

```
- name: Gather erlang cookie from first EMQ node
  command: cat /var/lib/emqx/.erlang.cookie
  when: emq_node_id | int == 1
  register: emq_erlang_cookie
```

The fetched value is unknown to other hosts. To change this, set fact on all
hosts:

```
- name: Set erlang_cookie fact
  set_fact:
    erlang_cookie: "{{ hostvars[emq_first_node]['emq_erlang_cookie']['stdout'] }}"
```

Finally, set the value of the variable on all other hosts:

```
- name: Set erlang cookie
  lineinfile:
    path: /var/lib/emqx/.erlang.cookie
    line: "{{ erlang_cookie }}"
    regexp: '\A.*'
  when: emq_node_id | int > 1
  register: erlang_cookie_changed
```

## How to contribute

Thank you for reading so far. Did/do you happen to:  
- find any error or typo?
- have a more elegant answer, workaround or better solution?
- have any other Ansible questions?

If so, you are welcome to open pull request.
